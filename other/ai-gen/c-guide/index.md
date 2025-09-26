---
layout: page
title: C Language Basics | Jakub Smolik
---

This text was generated using Gemini (free version).

# C Language Basics

#### Purpose

This guide is for C# developers ready to deepen their understanding of computer science and the machine their code runs on. While C# and the .NET runtime offer incredible power and productivity through abstraction and managed memory, a fundamental layer of the system remains. C is that layer. By learning C, you'll gain a unique perspective on how programs are compiled, how data is represented in memory, and how your high-level abstractions, like classes and garbage collection, are built upon a low-level foundation. This is not about abandoning C# but about becoming a more complete and versatile programmer.

#### Target Audience

The primary audience for this guide is **experienced C# and .NET developers**. You already understand core programming principles: control flow, data structures, and object-oriented concepts. We won't waste time explaining what a `for` loop is. Instead, we'll focus on the differences and similarities between C and C#. We will constantly draw parallels, contrasting C's manual memory management with C#'s garbage collection, its raw pointers with C#'s managed references, and its static compilation with .NET's JIT and IL. The goal is to leverage your existing knowledge to build a new, low-level mental model.

#### Structure

This guide is structured into four main parts:

- **[Part I](./part1.md): C Fundamentals:** Setting up your environment, writing your first C program, and understanding the basic syntax and semantics of C.
- **[Part II](./part2.md): Mastering Memory and Pointers:** Diving deep into pointers, arrays, C strings, and dynamic memory allocation, with a focus on how these concepts differ from C#.
- **[Part III](./part3.md): Advanced Data and Operations:** Exploring structures, unions, enums, bitwise operations, and advanced type system features.
- **[Part IV](./part4.md): Practical Tooling and Resources:** Learning how to debug C programs, use common tools, and find further resources for continued learning.

#### Learning Outcomes

By the end of this guide, you will be able to:

- Write, compile, and debug C programs from the command line.
- Master the concepts of pointers, pointer arithmetic, and manual memory allocation and deallocation.
- Understand the difference between the stack and the heap.
- Safely work with C strings and aggregate data types like `struct` and `union`.
- Develop a strong intuition for program performance and memory layout, skills that will make you a better programmer in any language.

## Table of Contents

### [Part I: C Fundamentals](./part1.md)

#### 1. Getting Started (your first C program)

- 1.1. C History, Standards, and Motivation
- 1.2. Installing a C Toolchain on Windows (MinGW/MSVC)
- 1.3. Installing a C Toolchain on Linux (GCC/Clang)
- 1.4. Choosing an IDE: VS Code, CLion, Visual Studio
- 1.5. Compiling and Running "Hello, World!"
- 1.6. Basic Coding Style and Formatting

#### 2. Language Primitives & Expressions (the building blocks)

- 2.1. Fundamental Integer and Floating-Point Types
- 2.2. Literals: `char`, `int`, `double`, and Suffixes
- 2.3. Operators, Precedence, and Expressions
- 2.4. The Ternary Conditional Operator (`? :`)
- 2.5. Type Conversions: C# `(int)x` vs C's Type Casting
- 2.6. Negative Numbers: Two's Complement Representation

#### 3. Functions & Program Structure (organizing your code)

- 3.1. Function Declaration vs. Definition
- 3.2. Header Files (`.h`) vs. Source Files (`.c`)
- 3.3. Scope and Lifetime of Local Variables
- 3.4. The `main` Function: `argc` and `argv`

### [Part II: Mastering Memory and Pointers](./part2.md)

#### 4. Pointers & Arrays (the heart of C memory management)

- 4.1. Understanding Memory Addresses
- 4.2. Pointers: Referencing (`&`) and Dereferencing (`*`)
- 4.3. Pointer Arithmetic
- 4.4. Arrays: Declaration and Initialization
- 4.5. The Array-Pointer Relationship
- 4.6. Null Pointers vs. C# `null`

#### 5. C Strings (working with text)

- 5.1. NUL-Terminated Character Arrays
- 5.2. String Literals
- 5.3. Common Pitfalls: Buffer Overflows and Off-by-One Errors
- 5.4. Standard Library String Functions (`<string.h>`)

#### 6. Dynamic Memory Allocation (manual heap management)

- 6.1. The Heap vs. The Stack
- 6.2. `malloc`, `calloc`, `realloc`, and `free`
- 6.3. Manual Memory Management vs. C# Garbage Collection (GC)
- 6.4. Best Practices: Checking for `NULL`, Avoiding Memory Leaks

### [Part III: Advanced Data and Operations](./part3.md)

#### 7. Aggregate Data Types (creating custom structures)

- 7.1. `struct`: Defining Custom Types
- 7.2. C `struct` vs. C# `struct`/`class` (Value vs. Reference Semantics)
- 7.3. Pointers to Structures (`->` operator)
- 7.4. `union`: Sharing Memory for Different Types
- 7.5. Bit Fields for Memory Optimization

#### 8. Advanced Type System Features (refining your types)

- 8.1. `const` for Read-Only Data
- 8.2. `enum` for Named Constants
- 8.3. `typedef` for Creating Type Aliases
- 8.4. Reading Complex Declarations (cdecl)

#### 9. Bitwise Operations (low-level data manipulation)

- 9.1. Bitwise Operators: `&`, `|`, `^`, `~`
- 9.2. Bit-Shifting Operators: `<<`, `>>`
- 9.3. Common Use Cases: Flags and Masks

### [Part IV: Practical Tooling and Resources](./part4.md)

#### 10. Debugging & Tooling (finding and fixing bugs)

- 10.1. Reading and Understanding Compiler Warnings (`-Wall`)
- 10.2. Using an IDE Debugger (Breakpoints, Step-Through)
- 10.3. Command-Line Debugging with GDB
- 10.4. Memory Debugging with Valgrind or AddressSanitizer (ASAN)

#### 11. Appendices & Further Reading

- 11.1. Common Compiler Flags (GCC/Clang)
- 11.2. Recommended IDEs and Toolchains
- 11.3. C Standard Quick Reference (C99/C11)
- 11.4. Troubleshooting Checklist for Common Errors
