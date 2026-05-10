---
tags:
  - big_data
  - distributed_systems
  - hadoop
summary: HDFS, MapReduce and YARN
use_cases:
  - indexing
  - etl
  - batch_processing
---

# Hadoop

## Overview

- Distributed system for large-scale data processing
- Core components:
	- HDFS (storage)
	- MapReduce (compute)
	- YARN (resource management)
- Designed for scalability, fault tolerance, and high throughput

---

## HDFS (Hadoop Distributed File System)

### Overview

- Distributed file system in Hadoop
- Designed for large-scale data storage (TB–PB)
- Optimized for high throughput, not low latency
- Designed for ==batch processing== (offline)

### Core Concepts

**Block**
- Files are split into fixed-size blocks (default: 128MB)
- Blocks are the unit of storage and replication

**Replication**
- Each block has multiple replicas (default: 3)
- Ensures fault tolerance
- Example:
	Block1 -> DN1, DN2, DN3

**Architecture**
- NameNode
	- Manages metadata:
		file -> block mapping
		block -> DataNode mapping
	- Does not store actual data

- DataNode
	- Stores block data
	- Periodically sends to NameNode:
		heartbeat (liveness)
		block report (stored blocks)

### Read and Write

**Write**
1. Client requests block placement from NameNode
2. NameNode returns DataNode pipeline (e.g., DN1 -> DN2 -> DN3)
3. Client writes to DN1
4. Data flows DN1 -> DN2 -> DN3
5. ACK flows back from DN3 -> DN2 -> DN1 -> Client

**Read**
1. Client requests block locations from NameNode
2. Reads from nearest DataNode (based on network topology: local > same rack > cross rack)

### Key Mechanisms

**Heartbeat and Block Report**
- DataNode regularly reports status to NameNode
- Used for failure detection and metadata update

**Fault Tolerance**
- DataNode failure: missing replicas are recreated
- NameNode <span class="abbr" data-title="High Availability">HA</span>: active and standby NameNodes

**Rack Awareness**
- Replicas distributed across racks
- Improves reliability under rack failure

**Design Principles**
- Data locality (move compute to data)
- Sequential read/write optimization (read/write whole blocks sequentially, not random access)
- Horizontal scalability (scale out by adding more machines, not upgrading single node)

### Limitations

- Inefficient for small files
- No random write (append-only)
- High latency

---

## MapReduce

### Overview

- Distributed ==batch processing== model in Hadoop
- Designed for large-scale data processing on HDFS
- Based on divide-and-conquer

### Programming Model

**Execution Flow**
Input -> Split -> Map -> Shuffle -> Reduce -> Output

**Map**
- Input: <k1, v1>
- Output: <k2, v2>
- ==Mapper==: runs on one of the replica nodes of each block
- Processes data in parallel
- Example:
	`"hello world hello"` -> `(hello,1)` `(world,1)` `(hello,1)`

**Shuffle**
- Input: <k2, v2>
- Output: <k2, list(v2)>
- Partition: decide which reducer handles a key (compute by mapper)
	`(hello,1)` -> Reducer 0  
	`(world,1)` -> Reducer 1  
	`(hello,1)` -> Reducer 0
- Sort: sort by key (partially sorted and spilled to disk by mapper, then merged and sorted by reducer)
	`(hello,1)` `(world,1)` `(hello,1)` -> `(hello,1)` `(hello,1)` `(world,1)`
- (Optional Combiner): local aggregation on mapper output
	`(hello,1)` `(world,1)` `(hello,1)` -> `(hello,2)` `(world,1)`
- Group: group values with same key (grouped by reducer)
	`(hello,1)` `(hello,1)` `(world,1)` -> `hello:[1,1]` `world:[1]`

**Reduce**
- Input: <k2, list(v2)>
- Output: <k3, v3>
- ==Reducer==: runs on any machines assigned by YARN
- Aggregates values by key
- Example:
	`hello:[1,1]` `world:[1]` -> `hello:2` `world:1`

### Key Mechanisms

**==Data Locality==**
- Map tasks run on nodes where data blocks are stored

**Fault Tolerance**
- Failed tasks are re-executed

**Parallelism**
- Map tasks run in parallel across blocks
- Reduce tasks run in parallel across keys

**Characteristics**
- Disk-based (intermediate results written to disk)
- High throughput, high latency
- Suitable for batch jobs

### Limitations

- Inefficient for iterative computation
- High I/O overhead
- Complex programming model

## YARN

### Overview

- Resource management layer in Hadoop
- Manages cluster resources (CPU, memory) and schedules applications
- Decouples resource management from computation

### Architecture

**ResourceManager**
- Global resource scheduler
- Allocates resources to applications

**NodeManager**
- Runs on each node
- Manages local resources and executes containers

**ApplicationMaster**
- One per application
- Negotiates resources with ResourceManager
- Coordinates task execution on NodeManagers

### Execution Flow

**Execution Flow**
Client -> ResourceManager -> ApplicationMaster -> Containers -> Tasks

1. Client submits application to ResourceManager
2. ResourceManager allocates a container and starts ApplicationMaster
3. ApplicationMaster requests resources from ResourceManager
4. ResourceManager allocates containers on NodeManagers
5. ApplicationMaster launches tasks in containers on NodeManagers
6. Tasks execute and report status to ApplicationMaster
7. ApplicationMaster finishes and releases resources

### Core Concepts

**Container**
- Unit of resource allocation (CPU, memory)
- Runs tasks (Map or Reduce)

**Scheduling**
- ResourceManager schedules containers based on policies (FIFO, Fair, Capacity)

### Key Mechanisms

**Resource Allocation**
- Applications request containers based on resource needs

**Fault Tolerance**
- Failed containers and ApplicationMaster can be restarted

**Scalability**
- Supports large clusters with many nodes

**Multi-tenancy**
- Multiple applications share cluster resources