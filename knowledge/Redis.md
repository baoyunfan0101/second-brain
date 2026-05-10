---
tags:
  - distributed_systems
  - redis
summary: data types, internal structures, persistence, cluster, distributed lock, caching, double write consistency
use_cases:
  - caching
  - ranking
---

# Redis

## Overview

**Redis (Remote Dictionary Server)** is an in-memory key-value data store, commonly used as a cache, database, and distributed system component.

## Core Features

- In-memory storage -> ultra-fast (microsecond latency)
- Rich data structures (not just key-value)
- Single-threaded core -> no lock contention
- Supports persistence and distributed deployment

## Data Types

Setup:
```java
import redis.clients.jedis.Jedis;

public class Main {
	public static void main(String[] args) {
		Jedis jedis = new Jedis("localhost", 6379);
		// call examples below
		jedis.close();
	}
}
```

### String

- Basic type
- Use cases: caching, counters

<span class="abbr" data-title="Redis Serialization Protocol">RESP</span>:
```java
// plain string
jedis.set("key", "value");           // SET key value       // "value"
String v = jedis.get("key");         // GET key             // "value"

// integer (str -> int -> compute -> str)
jedis.set("counter", "0");           // SET counter 0       // 0
jedis.incr("counter");               // INCR counter        // 1
jedis.incrBy("counter", 5);          // INCRBY counter 5    // 6
jedis.decr("counter");               // DECR counter        // 5
jedis.decrBy("counter", 5);          // DECRBY counter 5    // 0

// float (str -> double -> compute -> str)
jedis.incrByFloat("counter", 0.5);   // INCRBYFLOAT counter 0.5 // 0.5

// string ops
jedis.append("key", "123");          // APPEND key 123      // "value123"
int len = jedis.strlen("key");       // STRLEN key          // 8
String sub = jedis.getrange("key", 0, 2); // GETRANGE key 0 2 // "val"

// bit ops
jedis.setbit("bitkey", 1, true);     // SETBIT bitkey 1 1   // 0b01000000
boolean b = jedis.getbit("bitkey", 1); // GETBIT bitkey 1   // true
```

### List

- Doubly linked list
- Use cases: message queue

RSEP:
```java
jedis.lpush("list", "a", "b", "c");  // LPUSH list a b c   // ["c","b","a"]
jedis.rpush("list", "x");            // RPUSH list x       // ["c","b","a","x"]

String l1 = jedis.lpop("list");      // LPOP list          // "c"
String l2 = jedis.rpop("list");      // RPOP list          // "x"

System.out.println(jedis.lrange("list", 0, -1)); // LRANGE list 0 -1 // ["b","a"]
```

### Hash

- Field-value map
- Use cases: user profile

RSEP:
```java
jedis.hset("user:1", "name", "Alice");   // HSET user:1 name Alice
jedis.hset("user:1", "age", "20");       // HSET user:1 age 20

String name = jedis.hget("user:1", "name"); // HGET user:1 name // "Alice"
System.out.println(jedis.hgetAll("user:1")); // HGETALL user:1 // {name=Alice, age=20}

jedis.hdel("user:1", "age");            // HDEL user:1 age   // {name=Alice}
```

### Set

- Unordered, unique elements
- Use cases: deduplication

RSEP:
```java
jedis.sadd("set", "a", "b", "c", "a");  // SADD set a b c a // {"a","b","c"}

boolean exists = jedis.sismember("set", "a"); // SISMEMBER set a // true

System.out.println(jedis.smembers("set"));    // SMEMBERS set // {"a","b","c"}

jedis.srem("set", "b");                  // SREM set b       // {"a","c"}
```

### Sorted Set (ZSet)

- Ordered by score
- Use cases: ranking systems

```java
jedis.zadd("rank", 100, "Alice");        // ZADD rank 100 Alice
jedis.zadd("rank", 200, "Bob");          // ZADD rank 200 Bob

System.out.println(jedis.zrange("rank", 0, -1));   // ZRANGE rank 0 -1 // ["Alice","Bob"]
System.out.println(jedis.zrevrange("rank", 0, 1)); // ZREVRANGE rank 0 1 // ["Bob","Alice"]

Double score = jedis.zscore("rank", "Alice"); // ZSCORE rank Alice // 100

jedis.zrem("rank", "Bob");              // ZREM rank Bob   // ["Alice"]
```

## Internal Structures

### RedisObject

- Wraps every value
- Fields:
	- type (String / List / Hash / Set / ZSet)
	- encoding (actual storage)
	- ptr (pointer to data)

### SDS (Simple Dynamic String)

- Used by: String
- Length stored: `len = 5, buf = "hello"` -> $O(1)$ strlen
- Binary-safe: `buf = "a\0b"` -> not truncated
- Auto expand

### dict (Hash Table)

- Used by: ==keyspace== / Hash / Set / ZSet
- $O(1)$ lookup on average
- Incremental rehash: entries are moved gradually during resize

### SkipList

- Used by: ZSet
- $O(\log N)$ search / insert

```java
class Node {
    int score;
    String value;
    Node[] next;   // next[i]: pointer at level i

    Node(int score, String value, int level) {
        this.score = score;
        this.value = value;
        this.next = new Node[level]; // level = height of this node
    }
}

class SkipList {
    static final int MAX_LEVEL = 16;
    Node head = new Node(Integer.MIN_VALUE, "", MAX_LEVEL);

    // Randomly decide node height (higher level = sparser index)
    int randomLevel() {
        int level = 1;
        while (Math.random() < 0.5 && level < MAX_LEVEL) {
            level++;
        }
        return level;
    }

    // Insert a new node
    void insert(int score, String value) {
        Node[] update = new Node[MAX_LEVEL]; // track predecessors at each level
        Node cur = head;

        // 1. Find insertion position (top-down)
        for (int level = MAX_LEVEL - 1; level >= 0; level--) {
            while (cur.next[level] != null &&
                   cur.next[level].score < score) {
                cur = cur.next[level]; // move right
            }
            update[level] = cur; // record predecessor
        }

        // 2. Create new node with random height
        int newLevel = randomLevel();
        Node node = new Node(score, value, newLevel);

        // 3. Insert node by rewiring pointers
        for (int i = 0; i < newLevel; i++) {
            node.next[i] = update[i].next[i]; // link forward
            update[i].next[i] = node;         // link from predecessor
        }
    }

    // Delete a node by score
    boolean delete(int score) {
        Node[] update = new Node[MAX_LEVEL]; // track predecessors
        Node cur = head;

        // 1. Find target position
        for (int level = MAX_LEVEL - 1; level >= 0; level--) {
            while (cur.next[level] != null &&
                   cur.next[level].score < score) {
                cur = cur.next[level];
            }
            update[level] = cur;
        }

        Node target = cur.next[0]; // candidate node
        if (target == null || target.score != score) {
            return false; // not found
        }

        // 2. Remove node from each level it appears in
        for (int i = 0; i < target.next.length; i++) {
            update[i].next[i] = target.next[i]; // bypass target
        }

        return true;
    }
}
```

Example:
```text
Level 3:  HEAD ───────────────────> 30
           │                        │
Level 2:  HEAD ───────> 20 ───────> 30
           │            │           │
Level 1:  HEAD ─> 10 ─> 20 ───────> 30
           │      │     │           │
Level 0:  HEAD ─> 10 ─> 20 ─> 25 ─> 30 ─> 40
```

### ListPack (compact array)

- Used by: small data (Hash / List / ZSet)
- Sequential, memory-efficient: `["a","bb","ccc"]` -> `[1|a][2|bb][3|ccc]`

### QuickList

- Used by: List
- Linked list of ListPack
- Fast push/pop

### IntSet

- Used by: small integer Set
- Sorted integer array
- Auto upgrade (int16 -> int32 -> int64)

## Persistence

### RDB (Snapshot)

- Periodically saves data snapshot (`.rdb`)
- Fast recovery
- May lose recent data

**Commands**
- `SAVE`: ==synchronously== generate RDB snapshot (block)
- `BGSAVE`: ==asynchronously== generate RDB snapshot (fork child process)
- `LASTSAVE`: return the timestamp of the last successful RDB save
- `SHUTDOWN SAVE / NOSAVE`: force save before shutdown/shutdown without saving

**Configuration (Auto Trigger)**
- `save <seconds> <changes>`
	- Example: `save 900 1` -> trigger if at least 1 write in 900 seconds  
  - Multiple rules are ==ORed==

### AOF (Append Only File)

- Logs every write operation (`.aof`)
- Higher durability
- Can replay logs to restore data

**Rewrite**
- Minimal commands for current state
	- Example:
		```text
		set a 1
		set a 2
		set a 3
		```
		-> `set a 3`

## Distributed Features

### Overall Architecture

```text
Client
  |
Redis Cluster
  ├── Node A (Master, slots: 0–5000)
  │     └── Node A1 (Replica)
  ├── Node B (Master, slots: 5001–10000)
  │     └── Node B1 (Replica)
  ├── Node C (Master, slots: 10001–16383)
        └── Node C1 (Replica)
```

### Replication

**Topology**
- One Master -> multiple Replicas
- Master: client read & write
- Replica: client read (eventual consistency, may be stale)

**Sync**
- Asynchronous
- First: RDB (full sync)
- Then: command stream (incremental)

### Sentinel


**Monitoring**
- Periodically ping Master and Replicas:
	- SDOWN: one Sentinel detects node is down
	- ODOWN: quorum (most Sentinels) detects node is down

**Failover**
- Triggered when ==Master== is ODOWN:
	1. Select best Replica
	2. Promote to new Master
	3. Reconfigure other Replicas to follow new Master
	4. Respond to client queries when connection fails

- If a Replica is down:
	- Mark as SDOWN
	- System continues to work
	- Replica may rejoin and resync later

### Cluster

**Sharding**
- Total slots: 16384
- Each node maintains a global slot -> node mapping (via gossip)
- Client: key -> `slot = CRC16(key) % 16384` -> Master node
- If request hits wrong node:
	- Server replies `MOVED slot ip:port`
	- Client updates mapping and redirects request
- Slots are reassigned when scaling

## Distributed Lock

- One key -> one lock
- Flow: acquire lock -> do work -> release lock (if owner)
- Acquire:
	`SET lock:{key} value NX EX ttl`
	- `lock:{key}`: one per business key (e.g., `lock:user:1`)
	- `value`: unique identifier (e.g., UUID) to identify lock owner
	- `NX`: only set if key does not exist (acquire lock)
	- `EX ttl`: expiration time in seconds (auto release lock)
- Release: 
	```lua
	-- compare-and-delete
	if redis.call("get", KEYS[1]) == ARGV[1] then
	  return redis.call("del", KEYS[1])
	else
	  return 0
	end
	```
- How to set <span class="abbr" data-title="Time To Live">TTL</span>:
	- `ttl >= expected_time * 2 + buffer`
	- extend TTL periodically while holding lock

## Caching

- Cache-aside:
	- hit -> return
	- miss -> DB -> write back to cache

**Mechanisms**
- TTL: controls lifetime
	- lazy delete: delete the key when accessed if it is expired
	- periodic delete: background process scans and removes some expired keys
- Eviction: triggered when memory full
	- LRU: evict least recently used keys
	- LFU: evict least frequently used keys
	- random: evict keys randomly

### Cache Penetration

- Problem:
	- query non-existent key (==not in Redis or DB==) -> miss on Redis -> hit DB
- Solution:
	- cache null: `SET k "" EX 60` (cache empty value with 60s TTL)
	- Bloom filter: reject invalid keys before Redis

### Cache Breakdown (Hot Key)

- Problem:
	- hot key expires → many requests hit DB at once
- Solution:
	- mutex: acquire lock -> one request rebuilds cache -> others wait or skip
	- logical expire: keep and return old value, refresh in background
	- no expire for hot keys

### Cache Avalanche

- Problem:
	- many keys expire at same time / Redis restart -> DB overload
- Solution:
	- random TTL: `EX = base + rand()`
	- staggered preload
	- multi-level cache (local + Redis)

## Double Write Consistency

- **Delete:** write DB -> delete cache
	Why delete, not update:
	```text
	Thread A writes DB: value=1
	Thread B writes DB: value=2
	Thread B updates cache: value=2
	Thread A updates cache: value=1
	```
- **Delayed Double Delete:** write DB -> delete cache -> sleep -> delete cache again
	Why:
	```text
	Thread A: read cache miss -> read DB (old value)
	Thread B: write DB (new value) -> delete cache
	Thread A: write cache (old value)
	```

### Low Consistency Requirement

**Scenarios**
- counters (views, likes)
- rankings
- recommendations
- logs / analytics

**Approach
- write DB -> async delete cache (<span class="abbr" data-title="Message Queue">MQ</span> ensures execution; retry ensures success)

### High Consistency Requirement

**Scenarios**
- balance
- inventory
- orders
- payments

**Approach**
- DB as source of truth
- acquire lock(key) -> read/write DB -> delete/update cache -> release lock

## Why Redis is Fast

1. In-memory operations
	no disk I/O
2. Single-threaded + I/O multiplexing
	one main thread in a Redis node executes all commands, while I/O multiplexing handles many connections without blocking
3. Efficient data structures
	SDS/dict/SkipList/ListPack/QuickList/IntSet
4. No lock overhead
	no lock contention or thread context switching