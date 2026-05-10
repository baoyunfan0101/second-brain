---
tags:
  - data-structures
  - trees
summary: AVL Tree vs. Red-Black Tree structures, rotations, rebalancing, coloring
use_cases:
  - ordering
  - indexing
---

# AVL Tree and Red-Black Tree

## Overview

- Both AVL Tree and Red-Black Tree are self-balancing <span class="abbr" data-title="Binary Search Trees">BST</span>
- Guarantee $O(\log n)$ height
- Maintain balance via rotations
- Support ordered operations (search, insert, delete)

## AVL Tree

### Properties

- A strictly balanced BST
- For every node: `|height(left) - height(right)| <= 1`
- Height is tightly bounded: `O(log n)`
- Requires frequent rotations

### Node Structure

```java
class AVLNode<K, V> {
    K key;
    V value;

    int height;

    AVLNode<K, V> left;
    AVLNode<K, V> right;
}
```

### Balance Mechanism

`balance = left.height - right.height`

- `balance == -1 || balance == 0 || balance == 1` -> balanced
- `balance > 1` -> left heavy
- `balance < -1` -> right heavy

### Rotations

1. LL: left heavy + left subtree is not right heavy
- `rotateRight`
```text
      A      B
     /      / \
    B   -> C   A
   / \        /
  C  (D)    (D)
```

2. RR: right heavy + right subtree is not left heavy
- `rotateLeft`
```text
  A          B
   \        / \
    B   -> A   C
   / \      \
 (D)  C     (D)
```

3. LR: left heavy + left subtree is right heavy
- `rotateLeftRight`
```text
    A          A      C
   /          /      / \
  B          C      B   A
   \    ->  / \  ->    /
    C      B   D      D
   / \      \
 (E)  D     (E)
```

4. RL: right heavy + right subtree is left heavy
- `rotateRightLeft`
```text
    A      A          C
     \      \        / \
      B      C      A   B
     /  ->  / \  ->  \
    C      D   B      D
   / \        /
  D  (E)    (E)
```

### Rebalance

```java
AVLNode<K, V> rebalance(AVLNode<K, V> node) {
    updateHeight(node);

    int balance = height(node.left) - height(node.right);

    // LL: left heavy + left subtree is not right heavy
    if (balance > 1 && height(node.left.left) >= height(node.left.right)) {
        return rotateRight(node);
    }

    // LR: left heavy + left subtree is right heavy
    if (balance > 1 && height(node.left.left) < height(node.left.right)) {
        node.left = rotateLeft(node.left);
        return rotateRight(node);
    }

    // RR: right heavy + right subtree is not left heavy
    if (balance < -1 && height(node.right.right) >= height(node.right.left)) {
        return rotateLeft(node);
    }

    // RL: right heavy + right subtree is left heavy
    if (balance < -1 && height(node.right.right) < height(node.right.left)) {
        node.right = rotateRight(node.right);
        return rotateLeft(node);
    }

    return node;
}
```

### Search

```java
V search(AVLNode<K, V> node, K key) {
    if (node == null) {
        return null;
    }

    if (key == node.key) {
        return node.value;
    }

    if (key < node.key) {
        return search(node.left, key);
    } else {
        return search(node.right, key);
    }
}
```

### Insert

```java
AVLNode<K, V> insert(AVLNode<K, V> node, K key, V value) {
    node = bstInsert(node, key, value);

    return rebalance(node);
}
```

### Delete

```java
AVLNode<K, V> delete(AVLNode<K, V> node, K key) {
    node = bstDelete(node, key);

    if (node == null) {
        return null;
    }

    return rebalance(node);
}
```

## Red-Black Tree

### Definition & Properties

- A loosely balanced BST using color properties
- Root is black
- Red nodes cannot have red children
- Every path has same number of black nodes (black-height)

### Node Structure

```java
class RBNode<K, V> {
    K key;
    V value;

    boolean red;

    RBNode<K, V> left;
    RBNode<K, V> right;
    RBNode<K, V> parent;
}
```

### Insert Balance Mechanism

- Uncle is red -> recoloring
- Uncle is black -> rotations

#### Recoloring

1. parent is left child of grandparent
- recolor: parent -> black, uncle -> black, grandparent -> red
```text
        g(B)             g(R)
        /  \             /  \
     p(R)  u(R) ->    p(B)  u(B)
     /                /
  x(R)             x(R)
```

2. parent is right child of grandparent
- recolor: parent -> black, uncle -> black, grandparent -> red
```text
     g(B)             g(R)
     /  \             /  \
  u(R)  p(R)    -> u(B)  p(B)
           \                \
           x(R)             x(R)
```

#### Rotations

1. LL: parent is left child of grandparent + node is left child of parent
- `rotateRight`
```text
        g(B)          p(B)
        /  \          /  \
     p(R)  u(B) -> x(R)  g(R)
     /  \                /  \
  x(R)   T1            T1   u(B)
```

2. RR: parent is right child of grandparent + node is right child of parent
- `rotateLeft`
```text
     g(B)                p(B)
     /  \                /  \
  u(B)  p(R)    ->    g(R)  x(R)
        /  \          /  \
      T1   x(R)    u(B)   T1
```

3. LR: parent is left child of grandparent + node is right child of parent
- `rotateLeftRight`
```text
     g(B)            g(B)          x(B)
     /  \            /  \          /  \
  p(R)  u(B)      x(R)  u(B)    p(R)  g(R)
     \       ->   /  \       ->    \  /  \
     x(R)       p(R)  T2          T1  T2 u(B)
     /  \          \
   T1    T2         T1
```

4. RL: parent is right child of grandparent + node is left child of parent
- `rotateRightLeft`
```text
     g(B)          g(B)                x(B)
     /  \          /  \                /  \
  u(B)  p(R)    u(B)  x(R)          g(R)  p(R)
        /    ->       /  \    ->    /  \  /
     x(R)           T2   p(R)    u(B) T2  T1
     /  \                /
   T2    T1            T1
```

#### Rebalance (Insert)

```java
void fixAfterInsert(RBNode<K, V> node) {
    while (node != root && node.parent.red) {
        RBNode<K, V> parent = node.parent;
        RBNode<K, V> grandparent = parent.parent;

        // parent is left child of grandparent
        if (parent == grandparent.left) {
            RBNode<K, V> uncle = grandparent.right;

            // Recoloring: parent is red + uncle is red
            if (isRed(uncle)) {
                parent.red = false;
                uncle.red = false;
                grandparent.red = true;
                node = grandparent;
            }

            // LR: parent is left child + node is right child + uncle is black
            else if (node == parent.right) {
                node = parent;
                rotateLeft(node);
            }

            // LL: parent is left child + node is left child + uncle is black
            else {
                parent.red = false;
                grandparent.red = true;
                rotateRight(grandparent);
                break;
            }
        }

        // parent is right child of grandparent
        else {
            RBNode<K, V> uncle = grandparent.left;

            // Recoloring: parent is red + uncle is red
            if (isRed(uncle)) {
                parent.red = false;
                uncle.red = false;
                grandparent.red = true;
                node = grandparent;
            }

            // RL: parent is right child + node is left child + uncle is black
            else if (node == parent.left) {
                node = parent;
                rotateRight(node);
            }

            // RR: parent is right child + node is right child + uncle is black
            else {
                parent.red = false;
                grandparent.red = true;
                rotateLeft(grandparent);
                break;
            }
        }
    }

    root.red = false;
}
```

### Delete Balance Mechanism

1. Deleted node is red
- replace it with one of its children (if any)
- no red-black fix is needed

2. Deleted node is black
- replace it with one of its children (if any)
	1. if replacement is red, recolor it black
	2. if replacement is black or null, ==double black== appears

**double black:** treat current position as extra black
- `x`: current double black node
- `s`: sibling of `x`
- `n` (near nephew): sibling's child closer to `x`
- `f` (far nephew): sibling's child farther from `x`

#### Fix Double Black (If `x` is Left Child of Parent)

1. **Case 1: Sibling is red**
- `rotateLeft(p)`
- recolor: sibling -> black, parent -> red
- convert to a black sibling case

```text
     p(B)                s(B)
     /  \                /  \
  x(DB) s(R)    ->    p(R)  f(B)
        /  \          /  \
     n(B)  f(B)    x(DB) n(B)
```

2. **Case 2: Sibling is black + both nephews are black**
- recolor: sibling -> red
- move double black upward

```text
     p(?)             p(DB)
     /  \             /  \
  x(DB) s(B)    -> x(B)  s(R)
        /  \             /  \
     n(B)  f(B)       n(B)  f(B)
```

3. **Case 3: Sibling is black + near nephew is red + far nephew is black**
- `rotateRight(s)`
- recolor: near nephew -> black, sibling -> red
- convert to Case 4

```text
     p(?)             p(?)
     /  \             /  \
  x(DB) s(B)    -> x(DB) n(B)
        /  \                \
     n(R)  f(B)             s(R)
```

4. **Case 4: Sibling is black + far nephew is red**
- `rotateLeft(p)`
- recolor: sibling -> parent's color, parent -> black, far nephew -> black
- double black is resolved

```text
     p(?)                s(?)
     /  \                /  \
  x(DB) s(B)    ->    p(B)  f(B)
        /  \          /  \
     n(?)  f(R)    x(B)  n(?)
```

#### Rebalance (Delete)

```java
void fixAfterDelete(RBNode<K, V> node) {
    while (node != root && isBlack(node)) {
        RBNode<K, V> parent = node.parent;

        // node is left child of parent
        if (node == parent.left) {
            RBNode<K, V> sibling = parent.right;

            // Case 1: sibling is red
            if (isRed(sibling)) {
                sibling.red = false;
                parent.red = true;
                rotateLeft(parent);
                sibling = parent.right;
            }

            // Case 2: sibling is black + both nephews are black
            if (isBlack(sibling.left) && isBlack(sibling.right)) {
                sibling.red = true;
                node = parent;
            }

            // Case 3 & 4: sibling is black + at least one nephew is red
            else {
                // Case 3: near nephew is red + far nephew is black
                if (isBlack(sibling.right)) {
                    sibling.left.red = false;
                    sibling.red = true;
                    rotateRight(sibling);
                    sibling = parent.right;
                }

                // Case 4: far nephew is red
                sibling.red = parent.red;
                parent.red = false;
                sibling.right.red = false;
                rotateLeft(parent);
                node = root;
            }
        }

        // node is right child of parent
        else {
            RBNode<K, V> sibling = parent.left;

            // Case 1: sibling is red
            if (isRed(sibling)) {
                sibling.red = false;
                parent.red = true;
                rotateRight(parent);
                sibling = parent.left;
            }

            // Case 2: sibling is black + both nephews are black
            if (isBlack(sibling.left) && isBlack(sibling.right)) {
                sibling.red = true;
                node = parent;
            }

            // Case 3 & 4: sibling is black + at least one nephew is red
            else {
                // Case 3: near nephew is red + far nephew is black
                if (isBlack(sibling.left)) {
                    sibling.right.red = false;
                    sibling.red = true;
                    rotateLeft(sibling);
                    sibling = parent.left;
                }

                // Case 4: far nephew is red
                sibling.red = parent.red;
                parent.red = false;
                sibling.left.red = false;
                rotateRight(parent);
                node = root;
            }
        }
    }

    node.red = false;
}
```

### Search

```java
V search(RBNode<K, V> node, K key) {
    if (node == null) {
        return null;
    }

    if (key == node.key) {
        return node.value;
    }

    if (key < node.key) {
        return search(node.left, key);
    } else {
        return search(node.right, key);
    }
}
```

### Insert

```java
void insert(K key, V value) {
    RBNode<K, V> node = bstInsert(key, value);

    node.red = true;

    fixAfterInsert(node);

    root.red = false;
}
```

### Delete

```java
void delete(K key) {
    RBNode<K, V> node = searchNode(root, key);

    if (node == null) {
        return;
    }

    RBNode<K, V> removed = bstDelete(node);

    if (removed was black) {
        fixAfterDelete(replacementNode);
    }

    root.red = false;
}
```

## Typical Use Cases

### Red-Black Tree

- Widely used in standard libraries:
	- Java: `TreeMap`, `TreeSet`, `HashMap` (treeified buckets when `bucket size >= 8` and `HashMap capacity >= 64`)
	- C++: `std::map`, `std::set`