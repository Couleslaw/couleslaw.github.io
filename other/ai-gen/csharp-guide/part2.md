---
layout: default
title: C# Mastery Guide Part II | Jakub Smolik
---

[..](./index.md)

# Part II: Types, Memory, and Core Language Internals

Part II of the C# Mastery Guide delves into the core types and memory management principles that underpin C# programming. This section provides a deep understanding of how types are defined, how memory is managed, and the intricacies of the Common Type System (CTS) and the Common Language Runtime (CLR).

## Table of Contents

#### 3. The Common Type System (CTS): Values, References, and Memory Layout

- **3.1. The Stack and the Heap:** A definitive guide to where your data lives. Exploring method calls, stack frames, and object allocation strategies.
- **3.2. Value Types (`struct`):** In-depth analysis of their memory layout, storage implications on the stack or inline within objects, and performance characteristics.
- **3.3. Reference Types (`class`):** Understanding object headers (Method Table Pointer, Sync Block), and how object references are stored on the stack or within other objects.
- **3.4. The Great Unification: `System.Object` and Boxing:** How value types can be implicitly or explicitly converted to objects (boxing) and the associated performance cost of boxing and unboxing.
- **3.5. Scope and Lifetime:** Differentiating lexical scope in C# (compile-time visibility) from object lifetime managed by the Garbage Collector (runtime memory management).
- **3.6. Default Values and the `default` Keyword:** Understanding the default initialization values for built-in types (e.g., `int` to `0`, `bool` to `false`, reference types to `null`), and the `default` keyword for obtaining these values for any type.

#### 4. Memory Management and Garbage Collection

- **4.1. The .NET Generational Garbage Collector:** How the tracing GC works. Detailed explanation of Generations 0, 1, and 2, and the mark-and-compact algorithm.
- **4.2. The Large Object Heap (LOH):** Understanding why large objects (arrays, strings) are treated differently, their allocation patterns, and the problem of fragmentation on the LOH.
- **4.3. Finalization and `IDisposable`:** The `Dispose` pattern for deterministic cleanup of unmanaged resources vs. non-deterministic finalizers. Covers `using` statements, `using` declarations (C# 8), and `await using` for `IAsyncDisposable` (C# 8).
- **4.4. Weak References:** Using `WeakReference<T>` for scenarios like caching, preventing strong references from hindering garbage collection, and avoiding memory leaks.
- **4.5. Advanced GC:** Deep dive into Workstation vs. Server GC modes, concurrent collection, and tuning the GC behavior with `GCSettings` for specific application needs.

#### 5. Assemblies, Type Loading, and Metadata

- **5.1. Assembly Loading:** How the CLR resolves, locates, and loads assemblies during runtime, including the role of `AssemblyLoadContext` for isolation.
- **5.2. Organizing Code: Namespaces, File-Scoped Namespaces (C# 10), and Global Usings (C# 10):** How namespaces function as a compile-time construct, the benefits of file-scoped namespaces for conciseness, and the impact of global usings on project-wide imports.
- **5.3. Reflection and Metadata:** Reading and manipulating type information at runtime using `System.Reflection`. Understanding the performance cost and the immense power of late binding.
- **5.4. Dynamic Code Generation with `System.Reflection.Emit`:** Emitting Common Intermediate Language (IL) at runtime to dynamically create types, methods, and assemblies.
- **5.5. Runtime Type Handles and Type Identity:** Understanding the internal representation and significance of `RuntimeTypeHandle`, `RuntimeMethodHandle`, and `RuntimeFieldHandle` for unique type and member identification.
- **5.6. Attributes: Metadata for Control and Information** Common Usage and Core Behaviors: A deep dive into frequently used built-in attributes (e.g., `[Obsolete]`, `[Serializable]`, `[Conditional]`, `[MethodImpl]`, `[DllImport]`), explaining their purpose and how they influence the compiler, runtime, or external tools.
- **5.7. Custom Attributes: Definition, Usage, and Reflection:** How attributes are defined, applied to code elements, processed at compile-time by tools, and discovered/interpreted at runtime via reflection.

#### 6. Access Modifiers: Visibility, Encapsulation, and Advanced Scenarios

- **6.1. Fundamental Modifiers (`public`, `private`):** Their basic scope and usage within a type and within a project.
- **6.2. Assembly-level Modifiers (`internal`, `file` C# 11):** Controlling visibility across assembly boundaries, including the `InternalsVisibleTo` attribute for controlled internal exposure.
- **6.3. Inheritance-based Modifiers (`protected`, `private protected` C# 7.2, `protected internal`):** Nuances of access within class hierarchies, and the precise distinctions between `private protected` (accessible only within derived types in the same assembly) and `protected internal` (accessible within derived types in any assembly, or any type in the same assembly).
- **6.4. Default Access Levels:** What default access is applied to types and members if no modifier is explicitly specified.

---

---

## Where to Go Next

- [**Part I: The .NET Platform and Execution Model:**](./part1.md) Delving into the foundational environment of .NET and how C# code is compiled and executed.
- [**Part III: Core C# Types: Design and Deep Understanding:**](./part3.md) Mastering the essential building blocks of C# code, from object-oriented principles with classes and structs to modern type design and nullability.
- [**Part IV: Advanced C# Features: Generics, Patterns, LINQ, and More:**](./part4.md) Unlocking C#'s powerful and expressive features, delving into new programming paradigms, dynamic capabilities, and low-level compiler interactions.
- [**Part V: Concurrency, Performance, and Application Lifecycle:**](./part5.md) Covering critical aspects of building and maintaining high-performance C# applications, including asynchronous programming, optimization, debugging, and deployment.
- [**Part VI: Architectural Principles and Design Patterns:**](./par6.md) Understanding the overarching principles and established design patterns for structuring robust, maintainable, and scalable C# systems.
- [**Appendix:**](./appendix.md) A collection of resources, practical checklists, and a glossary to support the learning journey.
