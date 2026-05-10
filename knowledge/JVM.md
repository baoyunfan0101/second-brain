---
tags:
  - java
  - jvm
summary: JVM runtime memory areas, class loading, interpreter vs. JIT, GC, JVM tuning parameters
use_cases:
  - system_design
---

# JVM

## JDK

<span class="abbr" data-title="Java Development Kit">JDK</span> combines development tools and the runtime environment (<span class="abbr" data-title="Java Runtime Environment">JRE</span>).

**Components**
1. JRE
	- <span class="abbr" data-title="Java Virtual Machine">JVM</span>
	- Core class libraries (e.g., `java.lang.*`, `java.util.*`)

2. Development tools
	- `javac`: compile `.java -> .class`
	- `java`: run `.class`
	- `javadoc`: generate `.java` -> HTML documentation
	- `jar`: package `.class` -> `.jar`

## JVM

JVM is a runtime environment that executes Java bytecode (a platform-independent intermediate instruction set).

**Core responsibilities:**
- Class loading
- Memory management
- Code execution

**Pipeline:**
```text
.java -> compile -> .class (bytecode) -> JVM -> machine code
```

Know about [[GC|GC]].

## Runtime Data Areas

### Thread-Private

- Program Counter (PC): current instruction position
- Java Stack: method calls (stack frames)
- Native Method Stack: <span class="abbr" data-title="Java Native Interface">JNI</span> calls to non-Java code (e.g., C/C++)

**Characteristics:**
- Each thread has its own copy
- No sharing, no synchronization needed

### Thread-Shared

**Heap**
- Stores all objects
- Main area for GC

**Method Area / Metaspace (JDK 8+)**
- Class metadata (definition and structure of the class)
- Runtime constant pool (constants and symbolic references used at runtime)
- Static variables

JDK 8+: Metaspace (native memory)

## Class Loading Mechanism

**Lifecycle**
- Loading: read .class into memory
- Verification: ensure safety
- Preparation: allocate static variables (default values)
- Resolution: symbolic reference -> direct reference
- Initialization: execute static blocks

**Parent Delegation Model**
```text
Bootstrap ClassLoader <- Extension ClassLoader <- Application ClassLoader
```

- Bootstrap ClassLoader: loads core JDK classes (e.g., `java.lang.*`, `java.util.*`).
- Extension ClassLoader: loads JDK extension classes or platform extension libraries.
- Application ClassLoader: loads application classes and third-party dependencies from the classpath.

Delegate to parent first to:
- Prevent duplicate loading
- Ensure security (core classes not overridden)

## Execution Engine

**Interpreter**
- Used at program startup, first execution, and for cold code
- Executes bytecode instruction by instruction via dispatch to precompiled native routines
- Has high per-instruction overhead:
	- Dispatch to select the handler for each opcode
	- Branch to the handler code

**Compiler (<span class="abbr" data-title="Just-In-Time Compiler">JIT</span>)**
- Used for hot code (methods or loops executed many times)
- Compiles bytecode into optimized native machine code and caches it
- Has upfront compilation overhead:
	- Compilation from bytecode to native machine code
	- Applies optimizations (e.g., inlining, loop optimizations)
	- Stores compiled code in cache for reuse
- Has two compiler tiers:
	- C1 (client, fast compile, fewer optimizations)
	- C2 (server, slower compile, aggressive optimizations)

## JVM Tuning

1. **Heap size**
- `-Xms`: the heap size at JVM startup
	smaller -> more frequent resizing
	larger -> higher initial memory usage

- `-Xmx`: the maximum heap size
	smaller -> more frequent GC / <span class="abbr" data-title="Out Of Memory">OOM</span>
	larger -> longer GC pauses

2. **Young / Old generation**
- `-Xmn`: the size of Young Generation
	smaller -> more frequent Minor GC
	larger -> higher memory usage / longer Minor GC pauses

- `-XX:NewRatio`: the ratio between Old and Young
	higher -> smaller Young / more frequent Minor GC
	lower -> larger Young / higher memory usage

- `-XX:SurvivorRatio`: the ratio between Eden and Survivor
	higher -> smaller Survivor / earlier promotion to Old
	lower -> larger Survivor / longer time in Young

3. **Object promotion**
- `-XX:MaxTenuringThreshold`: the max GC cycles before promotion
	lower -> earlier promotion / higher Old Gen pressure
	higher -> later promotion / higher Young Gen pressure

- `-XX:TargetSurvivorRatio`: target occupancy of Survivor space
	higher -> denser Survivor / higher promotion risk
	lower -> looser Survivor / higher memory overhead

4. **GC behavior**
- `-XX:+UseG1GC / -XX:+UseParallelGC / -XX:+UseZGC`: selects garbage collector
	Know about [[GC|JVM Garbage Collectors]].

- `-XX:MaxGCPauseMillis`: desired max GC pause (G1)
	lower -> shorter pauses / lower throughput
	higher -> longer pauses / higher throughput

- `-XX:InitiatingHeapOccupancyPercent`: heap usage to start GC
	lower -> earlier GC / higher GC overhead
	higher -> later GC / higher memory pressure

5. **GC logging**
- `-Xlog:gc*`: enables GC logs