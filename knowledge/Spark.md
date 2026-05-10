---
tags:
  - big_data
  - distributed_systems
  - spark
summary: RDD/DataFrame, DAG-based execution, lazy transformations/actions, shuffle mechanics and optimization
use_cases:
  - etl
  - streaming
  - ml
  - graph_processing
---

# Spark

## Overview

- Distributed data processing engine
- Designed for large-scale data analytics
- Runs on clusters (standalone, YARN, Kubernetes)
- Core idea: in-memory computation + <span class="abbr" data-title="Directed Acyclic Graph">DAG</span> execution

**Key Features**
- In-Memory Computing: faster than disk-based systems (e.g., MapReduce)
- Lazy Evaluation: transformations are not executed immediately
- Fault Tolerance: via lineage (recompute lost partitions)
- Unified Engine: supports batch, streaming, SQL, ML, graph

## Core Concepts

### RDD (Resilient Distributed Dataset)

- Distributed dataset with computation semantics

```python
from pyspark import SparkContext

# init SparkContext
sc = SparkContext("local", "test")

# rdd: a distributed dataset from an iterable
rdd = sc.parallelize([1, 2, 3, 4])

# rdd2: rdd + transformations (lazy, not executed) -> 
rdd2 = rdd.map(lambda x: x * 2).filter(lambda x: x > 4)

# action triggers execution, runs the whole DAG
result = rdd2.collect()
```

**Core Ideas**
- Immutable: cannot be modified, every operation returns a new RDD
- Partitioned: data is split into partitions, one partition = one parallel task
- Lazy evaluation: transformations are not executed immediately, only build a DAG
- Fault tolerance: stores lineage (how it was derived), and recomputes lost partitions

**Operations**
- Transformations: `map`, `filter`, `flatMap` (lazy, build logical plan)
- Actions: `count`, `collect`, `save` (eager, trigger execution)

### DataFrame

- Structured distributed dataset with explicit schema (columns + data types)

```python
from pyspark.sql import SparkSession

# init SparkSession
spark = SparkSession.builder.appName("test").getOrCreate()

# df: a structured distributed dataset with schema
df = spark.createDataFrame([(1, "a"), (2, "b")], ["id", "val"])

# df2: df + transformations (lazy, not executed)
df2 = df.filter("id > 1").select("val")

# action triggers execution, runs the whole DAG
result = df2.collect()
```

**Core Ideas**
- Declarative API (SQL-like)
- Query plan optimized by Catalyst Optimizer

**Operations**
- Transformations: `select`, `filter`, `groupBy`, `join` (lazy, build logical plan)
- Actions: `collect`, `show`, `count`, `write` (eager, trigger execution)

### Dataset

- Typed version of DataFrame

```scala
import org.apache.spark.sql.SparkSession

// init SparkSession
val spark = SparkSession.builder.appName("test").getOrCreate()
import spark.implicits._

// ds: a typed structured distributed dataset with schema
case class Item(id: Int, value: String)
val ds = Seq(Item(1, "a"), Item(2, "b")).toDS()

// ds2: ds + transformations (lazy, not executed)
val ds2 = ds.filter(_.id > 1).map(_.value)

// action triggers execution, runs the whole DAG
val result = ds2.collect()
```

## Execution Model

**Workflow**
Input -> Transformations -> DAG -> Execution -> Output

**DAG**
- Logical plan of transformations
- Optimized before execution
- Split into stages

**Execution
- Task: smallest execution unit, runs on a single partition
- Stage: set of tasks separated by shuffle boundaries

## Shuffle

- Data redistribution across nodes
- Cost: expensive (network + disk I/O)
- Happens in operations like:
	- `groupByKey`
	- `reduceByKey`
	- `join`

**Optimization**
- Avoid unnecessary shuffle (no cross-node data transfer)
- Reduce data size before shuffle (filter / select / pre-aggregation)
- Use `reduceByKey` / `aggregateByKey` instead of `groupByKey` (map-side combiner)
- Use broadcast join for small tables (broadcast small table to join with large table locally)
- Tune partition number (balance parallelism and per-task workload)
- Handle data skew (salting / AQE skew handling)
- Reduce spill and disk I/O (control intermediate data size)
- Prefer DataFrame / SQL (automatic optimization)
- Enable AQE (adaptive query optimization)

## Persistence (Caching)

- Store intermediate data in memory/disk
- Avoid recomputation

**Methods**
- `cache()` (default: memory)
- `persist(StorageLevel)`

## Cluster Architecture

**Driver**
- Program entry point
- Builds DAG
- Schedules tasks

**Executor**
- Runs tasks on worker nodes
- Stores data (cache)

**Cluster Manager**
- Resource manager (YARN / Kubernetes / Standalone)

## Streaming

- Spark supports streaming via micro-batch model

**Core Idea**
- Treat stream as an unbounded table
- Process data in small batches (micro-batch)
```text
stream -> micro-batches -> batch execution -> output
```

**Key Concepts**
- Input sources: Kafka / files
- Output sinks: console / database / storage
- Window: aggregate data over time intervals
- State: maintain intermediate results across batches

**Characteristics**
- Near real-time (seconds-level latency)
- Not true streaming (compared to Flink)