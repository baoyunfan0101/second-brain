---
tags:
  - data-structures
  - trees
summary: B-Tree vs. B+ Tree structures, search mechanisms, trade-offs
use_cases:
  - databases
  - indexing
---

# B-Tree and B+ Tree

## B-Tree

### Definition

- A multi-way balanced search tree
- Stores keys (as separators) and values in ==all nodes (internal + leaf)==
- Allows search to end at any level

### Node Structure

```java
abstract class BTreeNode<K, V> {
    List<K> keys;          // sorted keys (used as separators)
    List<V> values;        // values (same size as keys)
    List<BTreeNode<K,V>> children; // children pointers
    boolean isLeaf;
}
```

### Properties

- `keys.size() = k`
- `children.size() = k + 1` (if not leaf)
- all keys are sorted
- each subtree satisfies ordering constraints

### Search

```java
V search(BTreeNode<K,V> node, K key) {
    int i = 0;
    while (i < node.keys.size() && key > node.keys.get(i)) {
        i++;
    }
    if (i < node.keys.size() && key == node.keys.get(i)) {
        return node.values.get(i); // found in any node
    }
    if (node.isLeaf) return null;
    return search(node.children.get(i), key);
}
```

## B+ Tree

### Definition

- A variant of B-Tree optimized for storage systems
- Stores only keys (as separators) and child pointers in ==internal nodes==
- Stores all values only in ==leaf nodes==
- Requires search to always end at leaf level
- Connects leaf nodes via a linked list

### Node Structure

**Abstract Node**
```java
abstract class BPlusNode<K, V> {
    List<K> keys;  // keys
}
```

**Internal Node**
```java
class InternalNode<K, V> extends BPlusNode<K, V> {
    List<BPlusNode<K,V>> children; // children pointers
}
```

**Leaf Node**
```java
class LeafNode<K, V> extends BPlusNode<K, V> {
    List<V> values;      // values
    LeafNode<K,V> next;  // linked list
}
```

### Properties

- internal nodes: `keys.size() = k`, `children.size() = k + 1`
- leaf nodes store all values
- all leaves are at the same depth
- leaves are linked in sorted order

### Search

```java
V search(BPlusNode<K,V> node, K key) {
    if (node instanceof LeafNode) {
        LeafNode<K,V> leaf = (LeafNode<K,V>) node;
        for (int i = 0; i < leaf.keys.size(); i++) {
            if (leaf.keys.get(i).equals(key)) {
                return leaf.values.get(i);
            }
        }
        return null;
    }

    InternalNode<K,V> in = (InternalNode<K,V>) node;
    int i = 0;
    while (i < in.keys.size() && key >= in.keys.get(i)) {
        i++;
    }
    return search(in.children.get(i), key);
}
```

### Range Query

```java
List<V> rangeQuery(BPlusNode<K,V> root, K start, K end) {
    List<V> result = new ArrayList<>();

    LeafNode<K,V> leaf = findLeaf(root, start);

    while (leaf != null) {
        for (int i = 0; i < leaf.keys.size(); i++) {
            if (leaf.keys.get(i) >= start && leaf.keys.get(i) <= end) {
                result.add(leaf.values.get(i));
            }
            if (leaf.keys.get(i) > end) {
                return result;
            }
        }
        leaf = leaf.next;
    }
    return result;
}
```

## Performance

**B-Tree**
- Slightly faster for single key lookup (sometimes)
- Lower redundancy (no duplicated keys in internal nodes)

**B+ Tree**
- Higher fan-out (more keys per node) -> Lower tree height -> Fewer disk IO operations -> Better for disk-based systems
- Supports efficient range queries

## Typical Use Cases

**B-Tree**
- Rare in modern systems
- Some in-memory structures

**B+ Tree**
- Databases:
	- `MySQL` (`InnoDB`): a <span class="abbr" data-title="Relational Database Management System">RDBMS</span> (its default storage engine)
	- `PostgreSQL`: an open-source RDBMS
- File systems:
	- `NTFS`: a Windows file system
	- `APFS`: an Apple file system for macOS and iOS
- Key-value storage engines:
	- `RocksDB`: an embeddable LSM-tree-based key-value storage engine
	- `LevelDB`: an embeddable LSM-tree-based key-value storage engine