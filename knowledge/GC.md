---
tags:
  - memory
  - gc
  - java
summary: identification methods, collection algorithms, generational design, language comparison, JVM Garbage Collectors
use_cases:
  - system_design
  - programming
---

# GC

## Garbage Identification Methods

**Reference Counting**
- Each object maintains a reference count
- Count = 0 -> reclaim
- Pros: simple, immediate reclamation
- Cons: cannot handle cyclic references

**Reachability Analysis (Tracing <span class="abbr" data-title="Garbage Collection">GC</span>)**
- Start from GC Roots (stack variables, static fields, etc.)
- Reachable -> alive
- Unreachable -> garbage
- Used by most modern languages (Java, Python, Go)

## Collection Algorithms

**Mark-Sweep**
- Mark all reachable objects
- Sweep unmarked objects
- Cons: memory fragmentation

**Copying**
- Split memory into two regions
- Copy live objects to the other region
- Pros: no fragmentation, fast allocation
- Cons: wastes space

**Mark-Compact**
- Mark live objects
- Move them to one side (compact)
- Pros: no fragmentation, better space usage
- Cons: extra cost for moving objects

## Generational GC

- Key idea: most objects die young

**Memory Layout**
Heap
```text
Young Generation
  ├ Eden
  ├ Survivor S0
  └ Survivor S1

Old Generation
```

**Object Lifecycle**
```text
New -> Eden -> (survive Minor GC, age++) -> S0/S1 -> (age >= threshold or space pressure) -> Old
```

**Promotion Rules**
- Age threshold
- Survivor space pressure
- Large object directly to Old

**Strategy**
- Young generation: short-lived objects
	==Copying==
	Frequent & fast GC
- Old generation: long-lived objects
	==Mark-Sweep== / ==Mark-Compact==
	Less frequent & expensive GC

## Trigger Conditions

- Memory allocation failure / low memory
- Explicit request (e.g., `System.gc()`, not guaranteed)
- Runtime heuristics (thresholds, allocation rate, etc.)

## Language Comparison

**Java**
- JVM-managed, generational tracing GC with multiple collectors (G1, ZGC, etc.)

**JavaScript**
- Generational tracing GC

**Python**
- Reference counting + tracing GC

**Go**
- Concurrent, low-latency tracing GC

## JVM Garbage Collectors

### Serial Collector

- Model: single-threaded
- Algorithm: Mark-Compact (old), Copying (young)
- Behavior: simple, no concurrency, <span class="abbr" data-title="Stop-The-World">STW</span> (all application threads are paused)
- When to use: small heaps, single-core

### Parallel Collector (Throughput Collector)

- Model: multi-threaded
- Algorithm: Mark-Compact (old), Copying (young)
- Behavior: parallel GC threads, ==STW==, high throughput, requires tuning
- When to use: CPU-intensive, not latency-sensitive (e.g., batch processing)

### CMS (Concurrent Mark-Sweep)

- Model: mostly concurrent (deprecated and removed since JDK 14)
- Algorithm: ==Mark-Sweep== (old), Copying (young)
- Phases:
	- Initial Mark (STW, parallel): mark objects directly reachable from GC Roots
	- Concurrent Mark: traverse and mark all reachable objects
	- Remark (STW, parallel): re-scan dirty cards recorded by write barriers to fix changes in references during Concurrent Mark
	- Concurrent Sweep: reclaim unmarked objects
- Behavior: low pause, fragmentation

### G1 (Garbage First)

- Model: parallel + mostly concurrent (default collector since JDK 9)
- Core Idea:
	- Divides heap into multiple fixed-size regions
	- Selects regions with the ==highest garbage ratio== for collection (Garbage First)
- Algorithm: ==Copying== (old), Copying (young)
- Phases:
	- Initial Mark (STW, parallel)
	- Concurrent Mark
	- Remark (STW, parallel): process <span class="abbr" data-title="Snapshot-At-The-Beginning">SATB</span> buffers (==old references== recorded by write barriers during Concurrent Mark) to mark those missing references
	- Evacuation (STW, parallel): copy live objects to new regions
- Behavior: predictable pauses (selects a set of regions based on a ==pause-time prediction model== derived from historical statistics to meet target goals)
- When to use: large heaps (>4GB), general-purpose services

### ZGC (Z Garbage Collector)
  
- Model: almost fully concurrent
- Core Idea
	- ==Colored pointers==: carry metadata bits (e.g., mark state, relocation state) within object references
	- Load barrier: on each reference read, checks pointer state and ==remap it to the relocated address if needed==
- Algorithm: Copying
- Behavior: very short pauses (<10ms)
- When to use: latency-critical, very large heaps

## Shenandoah
  
- Model: almost fully concurrent (developed by Red Hat)
- Core Idea
	- ==Forwarding pointers==: each object contains a forwarding pointer to its new location after relocation
	- Load barrier: on each reference read, checks if the object has been relocated and ==follows the forwarding pointer to the new address if needed==
- Algorithm: Copying
- Behavior: very short pauses (<10ms)
- When to use: latency-sensitive systems

## JVM Tuning

Know about [[JVM|JVM Tuning]].