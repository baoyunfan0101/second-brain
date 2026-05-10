---
tags:
  - frontend
  - javascript
  - dom
summary: event loop, macrotask/microtask
use_cases:
  - web
  - scripting
---

# Event Loop

## Overview

The Event Loop is the core mechanism that allows JavaScript to handle asynchronous operations.

**Model**
- Single Thread (Call Stack)
- Task Queues
- Event Loop Scheduler

## Execution Flow

```text
┌─────────────────┐  callback push as a macrotask
│ Macrotask Queue │←───────────────────────────────┐
└────────┬────────┘                                │
         ↓  pull a macrotask (1)           Web APIs complete
┌────────────┐                                     │
│            │	sync code (2)            delegate to Web APIs
│ Call Stack │	Web APIs task ─────────────────────┘
│            │	Promise task ──────────────────────┐
└─────┬──────┘                           pause current function
      │ continue sync code                         │
      ↓                            remaining code  │  push as a microtask
┌────────────┐                                     ↓
│            │      pull a microtask      ┌─────────────────┐
│ Call Stack │←───────────────────────────┤ Microtask Queue │
│            │  repeat from (2)           └─────────────────┘
└─────┬──────┘
      ↓
repeat from (1)
```

## Task Types

**MacroTask**
- script (entire program)
- setTimeout / setInterval
- I/O (Node.js)
- UI rendering

**MicroTask**
- Promise.then / catch / finally
- queueMicrotask
- MutationObserver

## Execution Priority

Per Event Loop iteration:
1. Execute one macro task
2. Drain all microtasks
3. Render (browser)
4. Next iteration

## Example 1

```javascript
console.log(1);

setTimeout(() => {
  console.log(2);
}, 0);

Promise.resolve().then(() => {
  console.log(3);
});

console.log(4);
```

**Output**
```text
1 4 3 2
```

**Explanation**
- Sync: 1 -> 4
- Microtask: 3
- Macrotask: 2

## Example 2

```javascript
setTimeout(() => console.log("A"), 0);

Promise.resolve()
  .then(() => {
    console.log("B");
    setTimeout(() => console.log("C"), 0);
  })
  .then(() => console.log("D"));
```

**Output**
```
B D A C
```

**Explanation**
- Microtask: B -> D
- Macrotask: A -> C

## Browser vs. Node.js

**Browser**
- Simple loop: macro → micro → render

**Node.js**
Phases:
- timers
- I/O callbacks
- idle / prepare
- poll
- check (setImmediate)
- close callbacks