---
tags:
  - os
  - concurrency
summary: concepts of execution units and resource management in systems
use_cases:
  - multithreading
  - system_design
---

# Process and Thread

## Process

A process is the unit of resource allocation.

- Has its own independent address space (memory isolation)
- Has its own ==resources== (memory, files, handles, etc.)
- Inter-process communication (IPC) is relatively expensive

## Thread

A thread is the unit of CPU scheduling.

- Belongs to a process and shares its address space
- Has its own ==execution state== (program counter, stack, etc.)
- Thread context switching is relatively inexpensive (but requires synchronization, e.g., locks)