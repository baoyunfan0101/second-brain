---
tags:
  - os
  - memory
  - process
summary: process-level memory organization and execution model
use_cases:
  - programming
---

# Process Memory Layout

## Overview

A typical process memory is divided into the following regions:

- Stack
- Heap
- Code Segment (Text Segment)
- Data Segment
- Read-Only Data (Constant Area)

## Stack

**What it stores**
- Function call frames
	- Parameters
	- Local variables
	- Return address
	- Saved registers

**Lifetime**
- Exists during function execution
- Automatically created and destroyed

**Access / Performance**
- Very fast
- Limited size

**Concurrency**
- **Thread:** independent
- **Process:** not shared across processes

## Heap

**What it stores**
- Dynamically allocated memory (`malloc`, `new`)

**Lifetime**
- Controlled manually (C/C++) or by GC (in other languages)
- Exists until explicitly freed

**Access / Performance**
- Slower than stack
- Flexible size

**Concurrency**
- **Thread:** shared within a process
- **Process:** not shared across processes

## Code Segment (Text Segment)

**What it stores**
- Compiled machine instructions (program code)

**Lifetime**
- Entire program execution

**Properties**
- Read-only
- Fixed after program is loaded

**Concurrency**
- **Thread:** shared within a process
- **Process:** logically isolated per process (physically shareable)

## Data Segment

**What it stores**
- Global variables
- Static variables

**Lifetime**
- Entire program execution

**Properties**
- Read-write

**Concurrency**
- **Thread:** shared within a process
- **Process:** not shared across processes

## Read-Only Data (Constant Area)

**What it stores**
- String literals
- Compile-time constants
- Read-only global constants

**Lifetime**
- Entire program execution

**Properties**
- Read-only
- Can be deduplicated and shared

**Concurrency**
- **Thread:** shared within a process
- **Process:** logically isolated per process (physically shareable)