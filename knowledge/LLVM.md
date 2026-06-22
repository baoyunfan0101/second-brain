---
tags:
  - compiler
summary: LLVM, LLVM IR, LLVM vs. JVM
use_cases:
  - languages
---

# LLVM

## What is LLVM

LLVM is a compiler infrastructure framework, translating LLVM <span class="abbr" data-title="Intermediate Representation">IR</span> into machine code.

**Ahead-of-Time (AOT) Compilation**: The machine code is generated before the program runs.
```text
LLVM IR
	↓
Machine Code
	↓
Executable File
```

**Just-in-Time (JIT) Compilation**: The machine code is generated and executed at runtime.
```text
LLVM IR
	↓
Machine Code
	↓
Execute in Memory
```

Historically, LLVM stood for **Low Level Virtual Machine**, but today it is no longer considered a virtual machine in the same sense as the JVM.

## Compilation Pipeline

A typical compilation process:
```text
Source Code         # Original program source
    ↓
Lexer               # Characters -> Tokens
    ↓
Parser              # Tokens -> AST
    ↓
AST                 # Abstract Syntax Tree
    ↓
Semantic Analysis   # Type checking, Semantic validation
    ↓
IR                  # Intermediate Representation
    ↓
Optimization        # Improve performance, Reduce redundancy
    ↓
Machine Code        # Native CPU instructions
```

The frontend components (lexer, parser, and semantic analysis) are usually implemented by the language itself.

The backend is language-independent. Common backend infrastructures include:
- LLVM
- GCC
```text
C/C++ → Clang  ┐
Rust  → rustc  ├──→ LLVM IR
Swift → swiftc ┘
```

Different CPU architectures require different machine instructions. For example, the same LLVM IR may be translated into:
- x86 machine code
- ARM machine code
- RISC-V machine code
```text
LLVM IR
   ├──→ x86
   ├──→ ARM
   └──→ RISC-V
```

## LLVM IR

LLVM IR is the central abstraction within LLVM. All LLVM optimizations operate on IR.

Example:
```cpp
int add(int a, int b) {
    return a + b;
}
```

LLVM IR:
```llvm
define i32 @add(i32 %a, i32 %b) {
entry:
    %0 = add i32 %a, %b
    ret i32 %0
}
```

**Characteristics**:

1. Architecture Independence:
	Uses virtual registers which are mapped to real registers by the backend.

2. Static Single Assignment (SSA):
	Assigns each variable exactly once.

Example:
```c
int x = 1;
x = x + 1;
x = x + 2;
```

LLVM IR:
```llvm
%1 = add i32 1, 1
%2 = add i32 %1, 2
```

SSA significantly simplifies many compiler optimizations.

3. Assembly-Like but Higher Level:
	Resembles assembly language while remaining independent of any specific hardware architecture.

## LLVM vs. JVM

LLVM: Source portability
```text
Source Code
	↓
LLVM IR
	↓
x86/ARM/RISC-V Binary
```

JVM: Binary portability
```text
Java Source
      ↓
Bytecode
      ↓
 ┌────┼────┐
 ↓    ↓    ↓
x86  ARM  RISC-V
JVM  JVM  JVM
```