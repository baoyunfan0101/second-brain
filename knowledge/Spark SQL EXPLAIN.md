---
tags:
  - big_data
  - distributed_systems
  - spark
summary: Spark SQL EXPLAIN, AQE
use_cases:
  - etl
  - streaming
  - ml
  - graph_processing
---

# Spark SQL EXPLAIN

## Execution Planning Process

When a SQL query is submitted, Spark transforms it through several stages:

```text
SQL
↓
Parsed Logical Plan
↓
Analyzed Logical Plan
↓
Optimized Logical Plan
↓
Physical Plan
↓
Execution
```

### Parsed Logical Plan

```text
SQL -> Abstract Syntax Tree (AST)
```

- SQL syntax is checked.
- Table/column existence is not verified.
- Data types are unknown.

### Analyzed Logical Plan

```text
References -> Specific objects
```

- Table/column existence is not verified.
- Data types are resolved.
- Functions and aliases are resolved.

### Optimized Logical Plan

**Common optimizations:**

- Predicate Pushdown

```sql
SELECT name
FROM (
    SELECT *
    FROM employee
) t
WHERE salary > 10000;
```
↓
```sql
SELECT name
FROM (
    SELECT *
    FROM employee
    WHERE salary > 10000
) t;
```

- Column Pruning

```sql
SELECT name
FROM (
    SELECT name
    FROM employee
) t;
```
↓
```sql
SELECT name
FROM (
    SELECT name
    FROM employee
) t;
```

- Projection Pruning

```sql
SELECT name
FROM (
    SELECT name, salary
    FROM employee
) t;
```
↓
```sql
SELECT name
FROM employee;
```

- Constant Folding

```sql
SELECT 1 + 2;
```
↓
```sql
SELECT 3;
```

- Boolean Simplification

```sql
SELECT *
FROM employee
WHERE salary > 10000
    OR (salary > 10000 AND dept_id = 10);
```
↓
```sql
SELECT *
FROM employee
WHERE salary > 10000;
```

- Null Propagation

```sql
SELECT COALESCE(NULL, phone, email, 'unknown')
FROM customer;
```
↓
```sql
SELECT COALESCE(phone, email, 'unknown')
FROM customer;
```

### Physical Plan

```text
Optimized Logical Plan -> Physical Plan
```

- Physical execution operations such as `FileScan`, `Exchange`, and `Sort` are added.
- Concrete algorithms are selected for logical operations, such as `HashAggregate` for aggregation and `BroadcastHashJoin` or `SortMergeJoin` for join.

## Common Physical Operators

| Physical Operator         | Meaning                                                                   | Common Source                                   |
| ------------------------- | ------------------------------------------------------------------------- | ----------------------------------------------- |
| `FileScan`                | Reads required columns and rows from files such as Parquet or ORC         | `FROM`                                          |
| `Filter`                  | Keeps rows that satisfy a condition                                       | `WHERE`, `HAVING`                               |
| `Project`                 | Selects, removes, renames, or computes columns                            | `SELECT`                                        |
| `HashAggregate`           | Uses a hash table to perform grouped aggregation                          | `GROUP BY`, aggregate functions                 |
| `SortAggregate`           | Sorts rows by grouping keys before aggregation                            | Aggregation when hash aggregation is unsuitable |
| `BroadcastHashJoin`       | Broadcasts one side and builds a hash table for the join                  | Join with one small side                        |
| `ShuffledHashJoin`        | Shuffles both sides and builds a hash table within each partition         | Equi-join                                       |
| `SortMergeJoin`           | Shuffles and sorts both sides by the join keys, then merges matching rows | Large equi-join                                 |
| `BroadcastNestedLoopJoin` | Broadcasts one side and compares rows without equi-join keys              | Non-equi join, cross join                       |
| `Exchange`                | Redistributes data between partitions                                     | Shuffle, repartition, broadcast                 |
| `Sort`                    | Sorts rows within each partition or globally                              | `ORDER BY`, `SORT BY`, join preparation         |
| `Window`                  | Computes window functions over partitioned and ordered rows               | `OVER (PARTITION BY ... ORDER BY ...)`          |
| `Union`                   | Combines rows from multiple child plans                                   | `UNION ALL`                                     |
| `Generate`                | Expands one input row into multiple rows                                  | `EXPLODE`, `INLINE`                             |
| `CollectLimit`            | Collects only a limited number of rows to the driver                      | `LIMIT`                                         |
| `AdaptiveSparkPlan`       | Wraps a physical plan that AQE may modify during execution                | Adaptive Query Execution                        |

## Reading a Physical Plan

Spark displays a physical plan as a tree.

- Read the plan from bottom to top.
- Each operator receives data from its child operators.
- `+-` marks the last child of an operator.
- `:-` marks a child followed by another child at the same level.
- Operators with multiple inputs, such as joins, have multiple child branches.

**Example:**

```sql
SELECT
    c.name,
    COUNT(*) AS cnt
FROM orders o
JOIN customers c
    ON o.cid = c.id
WHERE o.amount > 100
GROUP BY c.name;
```

```text
== Physical Plan ==
AdaptiveSparkPlan
+- HashAggregate [name], [count(1) AS cnt]
   +- Exchange hashpartitioning(name, 200)
      +- HashAggregate [name], [partial_count(1)]
         +- Project [name]
            +- BroadcastHashJoin [cid], [id], Inner, BuildRight, false
               :- Project [cid]
               :  +- Filter (amount > 100)
               :     +- FileScan parquet orders
               +- BroadcastExchange HashedRelationBroadcastMode
                  +- Project [id, name]
                     +- FileScan parquet customers
```

Read from bottom to top.

- **Left Join Branch**

```text
FileScan parquet orders
↓
Filter (amount > 100)
↓
Project [cid]
```

The left branch reads `orders`, filters rows, and keeps the join key.

- **Right Join Branch**

```text
FileScan parquet customers
↓
Project [id, name]
↓
BroadcastExchange
```

The right branch reads `customers`, keeps the required columns, and broadcasts the data.

- **Join**

```text
Left branch
+
Right branch
↓
BroadcastHashJoin
↓
Project [name]
```

`BroadcastHashJoin` joins the two branches. `Project` keeps the column required by the aggregation.

- **Aggregation**

```text
HashAggregate [partial_count]
↓
Exchange hashpartitioning(name, 200)
↓
HashAggregate [count]
```

The first `HashAggregate` performs partial aggregation in each partition.

`Exchange` shuffles rows so that the same `name` is placed in the same partition. `200` is the configured number of shuffle partitions.

The second `HashAggregate` combines the partial results and produces the final count.

## Adaptive Query Execution (AQE)

AQE allows Spark to optimize the physical plan during query execution using runtime statistics.

```text
Initial Physical Plan
↓
Runtime Statistics
↓
Updated Physical Plan
```

### Common AQE Optimizations

- Coalescing Shuffle Partitions

```text
Exchange hashpartitioning(key, 200)
```

may be changed to fewer partitions when the shuffle data is small.

- Converting Join Strategy

```text
SortMergeJoin
```

may be changed to:

```text
BroadcastHashJoin
```

when one side is smaller than expected at runtime.

- Handling Skewed Partitions

A large skewed shuffle partition may be split into smaller partitions so that one task does not process significantly more data than the others.

### Enable AQE

AQE is enabled by default in modern Spark versions. It can be explicitly enabled with:

```sql
SET spark.sql.adaptive.enabled = true;
```

or:

```python
spark.conf.set("spark.sql.adaptive.enabled", "true")
```

### Adaptive Plan Status

When AQE is enabled, the physical plan is wrapped in `AdaptiveSparkPlan`.

`isFinalPlan=false` means the displayed plan may still be changed using runtime statistics.

## EXPLAIN Modes

| Mode                  | Description                                                 |
| --------------------- | ----------------------------------------------------------- |
| `EXPLAIN`             | Physical plan (default)                                     |
| `EXPLAIN EXTENDED`    | Parsed, analyzed, optimized logical plans and physical plan |
| `EXPLAIN FORMATTED`   | Structured, human-readable physical plan                    |
| `EXPLAIN COST`        | Includes estimated statistics and costs when available      |
| `DataFrame.explain()` | Prints the execution plan for a DataFrame                   |
