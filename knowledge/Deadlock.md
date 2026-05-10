---
tags:
  - os
  - concurrency
  - deadlock
summary: fundamental conditions and practical prevention strategies in concurrent systems
use_cases:
  - multithreading
  - system_design
  - database_transactions
---

# Deadlock

## Definition

A deadlock is a situation where multiple processes or threads are **blocked forever**, each waiting for resources held by others.

## Necessary Conditions

1. **Mutual Exclusion**
	A resource can only be held by one process/thread at a time.

2. **Hold and Wait**
	A process holds at least one resource and is waiting for additional resources.

3. **No Preemption**
	Resources cannot be forcibly taken away; they must be released voluntarily.

4. **Circular Wait**
	A cycle exists where each process is waiting for a resource held by the next.

> Essence: resource allocation forms a cycle

## Prevention Strategies

1. Break Circular Wait
	- Assign a global ordering to resources
	- Always acquire locks in a consistent order

> Typical approach: **lock ordering**

2. Break Hold and Wait
	- Request all resources at once
	- Or release held resources before requesting new ones

> Cons: low resource utilization

3. Break No Preemption
	- Allow resource preemption
	- If request fails, release current resources and retry

> Typical approach: **tryLock + retry/backoff**

4. Break Mutual Exclusion
	- Use shared resources where possible
	- Apply lock-free or concurrent data structures