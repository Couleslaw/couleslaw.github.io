---
layout: page
title: C# Mastery Guide | Jakub Smolik
---

This text was [generated](./prompt.md) using Gemini (free version).

[view as PDF document instead](csharp.pdf)

# C# Mastery Guide

#### Purpose

This guide is crafted for those who want to go beyond the surface of C# programming. The core aim is to demystify the inner workings of the language and the .NET runtime. We'll explore not just how to use C# features, but why they work the way they do, examining everything from compiler transformations to the intricacies of runtime execution. This deeper understanding will empower you to write code that's not only robust and maintainable but also highly performant.

#### Target Audience

Are you an experienced C# developer with a solid foundation, comfortable building .NET applications, but hungry for more? If you've ever found yourself wondering "what's truly happening under the hood?" or seeking to unlock new levels of performance and problem-solving, this guide is for you. It's designed for professionals ready to transform into C# and .NET experts, capable of tackling the most challenging architectural and optimization puzzles.

#### Structure

The guide is structured into six comprehensive parts, each building upon the last to provide a complete understanding of C# and the .NET platform:

- **[Part I](./part1.md): The .NET Platform and Execution Model:** Delving into the foundational environment of .NET and how C# code is compiled and executed.
- **[Part II](./part2.md): Types, Memory, and Core Language Internals:** Exploring the fundamental concepts of C# types, memory management, and the Common Language Runtime's inner workings.
- **[Part III](./part3.md): Core C# Types: Design and Deep Understanding:** Mastering the essential building blocks of C# code, from object-oriented principles with classes and structs to modern type design and nullability.
- **[Part IV](./part4.md): Advanced C# Features: Generics, Patterns, LINQ, and More:** Unlocking C#'s powerful and expressive features, delving into new programming paradigms, dynamic capabilities, and low-level compiler interactions.
- **[Part V](./part5.md): Concurrency, Performance, and Application Lifecycle:** Covering critical aspects of building and maintaining high-performance C# applications, including asynchronous programming, optimization, debugging, and deployment.
- **[Part VI](./part6.md): Architectural Principles and Design Patterns:** Understanding the overarching principles and established design patterns for structuring robust, maintainable, and scalable C# systems.
- **[Appendix](./appendix.md):** A collection of resources, practical checklists, and a glossary to solidify your understanding.

#### Learning Outcomes

By engaging with this guide, you will gain an unparalleled understanding of C# from its source code origins all the way to native execution. You'll develop the skills to diagnose complex runtime issues, elegantly apply advanced language features to solve challenging problems, and implement design patterns idiomatically within the C# ecosystem. Our goal is to elevate you from a proficient C# developer to a true language and platform virtuoso.

## Table of Contents

### [Part I: The .NET Platform and Execution Model](./part1.md)

#### [1. The .NET Landscape](./part1.md#1-the-net-landscape-1)

- 1.1. A Brief History of .NET
- 1.2. Runtimes & Implementations
- 1.3. SDKs, Runtimes, and Tooling
- 1.4. The Base Class Library (BCL) Philosophy

#### [2. The C# Compilation and Execution Model](./part1.md#2-the-c-compilation-and-execution-model-1)

- 2.1. A Compiled Language
- 2.2. Understanding CIL (Intermediate Language)
- 2.3. The Common Language Runtime (CLR) & Virtual Execution System (VES)
- 2.4. Just-In-Time (JIT) Compilation
- 2.5. Ahead-Of-Time (AOT) Compilation

### [Part II: Types, Memory, and Core Language Internals](./part2.md)

#### [3. The Common Type System (CTS): Values, References, and Memory Layout](./part2.md#3-the-common-type-system-cts-values-references-and-memory-layout-1)

- 3.1. The Stack and the Heap
- 3.2. The Great Unification: `System.Object`
- 3.3. Value Types (`struct`)
- 3.4. Reference Types (`class`)
- 3.5. Boxing and Unboxing
- 3.6. Scope and Lifetime
- 3.7. Default Values and the `default` Keyword

#### [4. Memory Management and Garbage Collection](./part2.md#4-memory-management-and-garbage-collection-1)

- 4.1. The .NET Generational Garbage Collector
- 4.2. The Large Object Heap (LOH)
- 4.3. Finalization and `IDisposable`
- 4.4. Weak References
- 4.5. Advanced GC

#### [5. Assemblies, Type Loading, and Metadata](./part2.md#5-assemblies-type-loading-and-metadata-1)

- 5.1. Assembly Loading
- 5.2. Organizing Code: Namespaces, File-Scoped Namespaces (C# 10), and Global Usings (C# 10)
- 5.3. Reflection and Metadata
- 5.4. Dynamic Code Generation with `System.Reflection.Emit`
- 5.5. Runtime Type Handles and Type Identity
- 5.6. Attributes: Metadata for Control and Information
- 5.7. Custom Attributes: Definition, Usage, and Reflection

#### [6. Access Modifiers: Visibility, Encapsulation, and Advanced Scenarios](./part2.md#6-access-modifiers-visibility-encapsulation-and-advanced-scenarios-1)

- 6.1. Fundamental Modifiers (`public`, `private`)
- 6.2. Assembly-level Modifiers (`internal`, `file` C# 11)
- 6.3. Inheritance-based Modifiers (`protected`, `private protected` C# 7.2, `protected internal`)
- 6.4. Default Access Levels

### [Part III: Core C# Types: Design and Deep Understanding](./part3.md)

#### 7. Classes: Reference Types and Object-Oriented Design Deep Dive

- 7.1. The Anatomy of a Class
- 7.2. Constructors Deep Dive
- 7.3. The `this` Keyword: Instance Reference and Context
- 7.4. Core Class Members: Properties, Indexers, and Events
- 7.5. Class Inheritance: Foundations and Basic Design
- 7.6. Polymorphism Deep Dive: `virtual`, `abstract`, `override`, and `new`
- 7.7. Virtual Dispatch and V-Tables
- 7.8. The `sealed` Keyword
- 7.9. Type Conversions: Implicit, Explicit, Casting, and Safe Type Checks
- 7.10. Method Resolution Deep Dive: Overloading and Overload Resolution
- 7.11. Operator Overloading and User-Defined Conversion Operators
- 7.12. Nested Types and Local Functions

#### 8. Structs: Value Types and Performance Deep Dive

- 8.1. The Anatomy, Memory Layout, and Boxing of a Struct
- 8.2. Struct Constructors and Initialization
- 8.3. Passing Structs: `in`, `ref`, `out` Parameters Revisited
- 8.4. Struct Identity: Implementing `Equals()` and `GetHashCode()`
- 8.5. High-Performance Types: `ref struct`, `readonly ref struct`, and `ref fields` (C# 11)
- 8.6. Structs vs. Classes: Choosing the Right Type

#### 9. Interfaces: Contracts, Implementation, and Modern Features

- 9.1. The Anatomy of an Interface
- 9.2. Interface Dispatch
- 9.3. Explicit vs. Implicit Implementation
- 9.4. Modern Interface Features

#### 10. Essential BCL Types and Interfaces: Design and Usage Patterns

- 10.1. Core Value Type Interfaces
- 10.2. Collection Interfaces
- 10.3. Resource Management Interfaces
- 10.4. Fundamental Types Deep Dive
- 10.5. Mathematical and Numeric Interfaces (Generic Math)

#### 11. Delegates, Lambdas, and Eventing: Functional Programming Foundations

- 11.1. Delegates Under the Hood
- 11.2. The `event` Keyword
- 11.3. Lambdas and Closures
- 11.4. Expression Trees

#### 12. Modern Type Design: Records, Immutability, and Data Structures

- 12.1. Record Classes (`record class` C# 9)
- 12.2. Record Structs (`record struct` C# 10)
- 12.3. `readonly` Modifier Beyond Fields
- 12.4. Immutability Patterns
- 12.5. Frozen Collections (`System.Collections.Immutable`)

#### 13. Nullability, Safety, and Defensive Programming

- 13.1. The `null` Keyword
- 13.2. Nullable Reference Types (NRTs) (C# 8+)
- 13.3. Nullable Value Types (`Nullable<T>`)
- 13.4. Null Coalescing and Conditional Operators
- 13.5. `required` Members (C# 11)
- 13.6. The `nameof` Operator
- 13.7. `throw` expressions (C# 7)

### [Part IV: Advanced C# Features: Generics, Patterns, LINQ, and More](./part4.md)

#### 14. Generics: Deep Dive into Type Parameterization

- 14.1. Generic Methods
- 14.2. Generic Classes
- 14.3. JIT Specialization vs. Code Sharing
- 14.4. Generic Constraints (`where`)
- 14.5. Generic Variance (`in` and `out`)
- 14.6. Generic Inheritance and Interface Implementation
- 14.7. Default Literal Expression (`default` revisited)
- 14.8. Generic Type Conversions
- 14.9. Advanced Generic Design Patterns (e.g., CRTP)

#### 15. Pattern Matching and Advanced Control Flow

- 15.1. Pattern Matching (C# 7+)
- 15.2. The Iterator Pattern: `IEnumerable` and `foreach`
- 15.3. Advanced Control Flow Statements
- 15.4. `checked` and `unchecked` Contexts

#### 16. Advanced Language Expressiveness and Design Features

- 16.1. Optional Parameters and Named Arguments
- 16.2. Extension Methods
- 16.3. `params` keyword and `params ReadOnlySpan<T>` (C# 13)
- 16.4. `scoped` Parameters and Locals (C# 11)
- 16.5. Collection Expressions (C# 12)
- 16.6. Raw String Literals (C# 11) and UTF-8 String Literals (C# 11)
- 16.7. Caller Argument Expression (C# 10)
- 16.8. `using static` directive and `Alias any type` (C# 12)

#### 17. LINQ: Language Integrated Query Deep Dive

- 17.1. LINQ Architecture and Design Principles
- 17.2. LINQ to Objects: Deferred Execution and Composition
- 17.3. Standard Query Operators Deep Dive
- 17.4. Custom Query Operators
- 17.5. LINQ and Expression Trees (Revisited)
- 17.6. Parallel LINQ (PLINQ) Overview
- 17.7. Tools for LINQ Development: LINQPad and Beyond

#### 18. Dynamic Programming and Interop

- 18.1. The `dynamic` Keyword
- 18.2. Inside the Dynamic Language Runtime (DLR)
- 18.3. Interop Scenarios

#### 19. Metaprogramming and Compiler Services

- 19.1. The Roslyn Compiler Platform
- 19.2. Source Generators (C# 9)
- 19.3. Roslyn Analyzers
- 19.4. Interceptors (C# 12-14, Experimental)
- 19.5. Low-level Memory Access and `unsafe` Code
- 19.6. `System.Runtime.CompilerServices.Unsafe` (Brief Overview)

### [Part V: Concurrency, Performance, and Application Lifecycle](./part5.md)

#### 20. Concurrency and Parallelism Fundamentals

- 20.1. Beyond the GIL: True Parallelism in .NET
- 20.2. Threads, Processes, and the Thread Pool
- 20.3. The Task Parallel Library (TPL)
- 20.4. Low-Level Synchronization Primitives

#### 21. Asynchrony Deep Dive: `async`/`await`, Cancellation, and Advanced Patterns

- 21.1. `async` and `await` Unwrapped
- 21.2. Asynchronous Error Handling
- 21.3. Cancellation Tokens
- 21.4. Advanced Asynchronous Patterns:
- 21.5. Best Practices for Asynchronous Programming

#### 22. Performance and Optimization

- 22.1. Finding Bottlenecks
- 22.2. Writing High-Performance C#
- 22.3. Optimizing for CPU Architecture: Core Affinity, Thread Counts, and NUMA
- 22.4. Benchmarking Done Right
- 22.5. Hardware-Level Optimization: SIMD and Intrinsics
- 22.6. Object Pooling and Re-use
- 22.7. Trimming, Linking, and NativeAOT
- 22.8. Interpolated String Handlers (C# 10)

#### 23. Testing, Debugging and Diagnostics

- 23.1. Essential Debugging Features in Visual Studio
- 23.2. Advanced Debugging Techniques
- 23.3. Code Testing Fundamentals and Best Practices
- 23.4. Production Diagnostics with `dotnet-dump`, `dotnet-counters`, and `dotnet-trace`
- 23.5. The Power of WinDbg and SOS
- 23.6. Structured Logging

#### 24. Packaging, Deployment, and Distribution

- 24.1. NuGet: The .NET Package Manager
- 24.2. The `csproj` File Deconstructed
- 24.3. Solution Files (`.sln`)
- 24.4. Deployment Models
- 24.5. Containerization

### [Part VI: Architectural Principles and Design Patterns](./part6.md)

#### 25. Design Patterns in Modern C#

- 25.1. Creational Patterns with C#:
- 25.2. Structural Patterns with C#:
- 25.3. Behavioral Patterns with C#:
- 25.4. Anti-Patterns and C# Idioms

#### 26. Architectural Principles: SOLID and Beyond

- 26.1. Introduction to Architectural Principles
- 26.2. The Single Responsibility Principle (SRP)
- 26.3. The Open/Closed Principle (OCP)
- 26.4. The Liskov Substitution Principle (LSP)
- 26.5. The Interface Segregation Principle (ISP)
- 26.6. The Dependency Inversion Principle (DIP)
- 26.7. Other Guiding Principles
- 26.8. Applying Principles in Practice

#### 27. Dependency Injection and Inversion of Control

- 27.1. Understanding Inversion of Control (IoC)
- 27.2. Dependency Injection (DI) as an IoC Pattern
- 27.3. The Role of DI Containers
- 27.4. Built-in .NET Core DI Container Deep Dive
- 27.5. Advanced DI Scenarios and Patterns
- 27.6. Lifetime Management Nuances and Pitfalls
- 27.7. Testing with Dependency Injection
- 27.8. Under the Hood of DI Containers (Briefly)

### [Appendix](./appendix.md)

- A.1. Glossary of Terms
- A.2. Practical Checklist
- A.3. Further Reading
- A.4. Essential .NET Libraries for Advanced Development (Brief Overview)
- A.5. Modern C# Features by Version
