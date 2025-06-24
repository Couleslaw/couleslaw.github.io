---
layout: default
title: C# Mastery Guide Part III | Jakub Smolik
---

[..](./index.md)

# Part III: Core C# Types: Design and Deep Understanding

Part III of the C# Mastery Guide focuses on the core types and language features that form the backbone of C# programming. This section provides a deep understanding of classes, structs, interfaces, and other fundamental constructs, exploring their design, memory management, and advanced features introduced in recent C# versions.

## Table of Contents

#### 7. Classes: Reference Types and Object-Oriented Design Deep Dive

- **7.1. The Anatomy of a Class:** Object Headers, understanding instance vs. static members, static constructors and their execution order, and the `beforefieldinit` flag.
- **7.2. Constructors Deep Dive:** Instance constructors, static constructors, and **Primary Constructors (C# 12)** for classes, detailing their execution flow and initialization semantics.
- **7.3. Properties, Indexers, and Events:** Compiler transformation of properties into `get; set;` methods, `init`-only setters, `required` members (C# 11) for compile-time initialization enforcement, the `field` keyword in property accessors (C# 11), and the underlying mechanics of `add_` / `remove_` methods for events.
- **7.4. Class Inheritance and Polymorphism:** How the CLR implements inheritance, method overriding, the `new` keyword, use of the `base` keyword, and object slicing considerations.
- **7.5. Virtual Dispatch and V-Tables:** A deep dive into virtual method tables (V-tables) and how the CLR uses them for dynamic dispatch when `override` is applied.
- **7.6. Operator Overloading and User-Defined Conversions:** The special `op_` methods the compiler looks for and how they are used to enable custom operator behavior and type conversions.
- **7.7. Nested Types and Local Functions:** How nested types and local functions are represented in IL, their scope rules, and implications for closures.
- **7.8. The `this` Keyword: Instance Reference and Context:** Comprehensive coverage of `this` for referring to the current instance, distinguishing from static members, constructor chaining, and other contextual uses.
- **7.9. The `sealed` Keyword:** Using `sealed` on types to prevent inheritance and on methods to prevent further overriding in derived classes.

#### 8. Structs: Value Types and Performance Deep Dive

- **8.1. The Anatomy of a Struct:** Detailed memory layout when stored on the stack vs. on the heap (when part of a class or boxed).
- **8.2. Structs and Memory Layout:** The implications of struct size, alignment, and padding on performance. How the CLR optimizes memory for structs.
- **8.3. Struct Constructors and Initialization:** Understanding default constructors (C# 11 auto-default initialization), the `readonly` modifier for structs and struct members, and field initialization. Includes **Primary Constructors (C# 12)** for structs.
- **8.4. Passing Structs: `in`, `ref`, `out` Parameters Revisited:** Detailed IL and performance implications of various parameter passing mechanisms for value types.
- **8.5. High-Performance Types: `ref struct`, `readonly ref struct`, and `ref fields` (C# 11):** Deep dive into stack-only types, the compiler's safety enforcement, and their critical role in high-performance APIs like `Span<T>`.
- **8.6. Structs vs. Classes:** A comprehensive comparison of features, typical use cases, and performance trade-offs, guiding optimal type choice.

#### 9. Interfaces: Contracts, Implementation, and Modern Features

- **9.1. The Anatomy of an Interface:** Understanding interfaces as contracts without state, and their representation in IL.
- **9.2. Interface Dispatch:** How interface method calls work via Interface Method Tables (IMTs), a mechanism distinct from class v-tables.
- **9.3. Explicit vs. Implicit Implementation:** How explicit implementation hides members and resolves naming conflicts when implementing multiple interfaces.
- **9.4. Modern Interface Features:**
  - **Default Interface Methods (DIM) (C# 8):** Adding default implementations to interfaces without breaking existing implementers.
  - **Static Abstract & Virtual Members in Interfaces (C# 11):** The foundational feature enabling Generic Math, allowing polymorphism on static methods for a wide range of types.

#### 10. Essential BCL Types and Interfaces: Design and Usage Patterns

- **10.1. Core Value Type Interfaces:** `IComparable<T>`, `IEquatable<T>`, `IFormattable`, `IParsable<T>`, `ISpanFormattable`, `ISpanParsable<T>` – their design, implementation, and compiler integration for type comparison, formatting, and parsing.
- **10.2. Collection Interfaces:** `IEnumerable<T>`, `ICollection<T>`, `IList<T>`, `IDictionary<TKey, TValue>`, `IReadOnlyCollection<T>`, `IReadOnlyList<T>`, `IReadOnlyDictionary<TKey, TValue>` – understanding their contracts, common implementations, and performance characteristics.
- **10.3. Resource Management Interfaces:** `IDisposable` (revisited) for deterministic resource cleanup, and its role in the `using` pattern.
- **10.4. Fundamental Types Deep Dive:**
  - `String`: Immutability, string pooling, `string` vs `char[]`, encoding, and performance considerations.
  - `DateTime` and `DateTimeOffset`: Understanding time zones, universal vs. local time, and best practices for date/time handling.
  - `Guid`: Structure, generation, and use cases for unique identifiers.
  - `Enum`: Underlying types, flags enums, and best practices for defining and using enumerations.
- **10.5. Mathematical and Numeric Interfaces (Generic Math):** A detailed look at interfaces like `IAdditionOperators<TSelf, TOther, TResult>`, `INumber<TSelf>`, and others introduced in C# 11, and how they enable generic algorithms over numeric types.

#### 11. Delegates, Lambdas, and Eventing: Functional Programming Foundations

- **11.1. Delegates Under the Hood:** First-class functions in C#, the `MulticastDelegate` class, and the internals of `Action`, `Func` and `Predicate` generic delegates.
- **11.2. The `event` Keyword:** Compiler generation of `add_` and `remove_` methods, ensuring thread safety for event subscriptions, and best practices for event design using `EventHandler<T>` and `EventHandler` delegates.
- **11.3. Lambdas and Closures:** How the compiler transforms lambda expressions into hidden classes, and the precise mechanics of variable capture (closures) and their performance implications.
- **11.4. Expression Trees:** Representing C# code as data structures (`System.Linq.Expressions.Expression`) for runtime interpretation and modification, primarily used by LINQ providers (e.g., LINQ to SQL).

#### 12. Modern Type Design: Records, Immutability, and Data Structures

- **12.1. Record Classes (`record class` C# 9):** Internals of compiler-generated methods for immutability, value-based equality, `with` expressions, and `ToString` overrides.
- **12.2. Record Structs (`record struct` C# 10):** Applying value-based equality, immutability, and `with` expressions to value types, and their memory layout considerations.
- **12.3. `readonly` Modifier Beyond Fields:** Deep dive into `readonly struct` and `readonly members` (C# 8), ensuring immutability at the type and member level.
- **12.4. Immutability Patterns:** Strategies for designing and enforcing immutable types in C#, including the Builder pattern for complex object creation.
- **12.5. Frozen Collections (`System.Collections.Immutable`):** The immutable collection types provided by `System.Collections.Immutable` and their benefits for concurrent and predictable data structures.

#### 13. Nullability, Safety, and Defensive Programming

- **13.1. The `null` Keyword:** How `null` references are represented in the CLR and the ubiquitous `NullReferenceException`.
- **13.2. Nullable Reference Types (NRTs) (C# 8+):** The compiler's flow analysis for nullability, nullable annotations (`?`), the null-forgiving operator (`!`), and `#nullable enable/disable` directives. The philosophical shift towards compile-time null safety.
- **13.3. Nullable Value Types (`Nullable<T>`):** The struct's internals, `HasValue`, `Value`, and implicit conversions to and from the underlying type.
- **13.4. Null Coalescing and Conditional Operators:** The `??`, `??=`, `?.`, and `!`. operators, their IL representation, and how they improve code safety and readability by handling nulls concisely.
- **13.5. `required` Members (C# 11):** Ensuring proper initialization of members at compile-time for objects, enforcing design contracts.
- **13.6. The `nameof` Operator:** Using `nameof` to obtain the string name of a variable, type, or member at compile time, improving refactoring safety and debugging/logging.
- **13.7. `throw` expressions (C# 7):** Using `throw` as an expression to make error handling more concise in ternary operations, lambda bodies, or property accessors.

---

---

## Where to Go Next

- [**Part I: The .NET Platform and Execution Model:**](./part1.md) Delving into the foundational environment of .NET and how C# code is compiled and executed.
- [**Part II: Types, Memory, and Core Language Internals:**](./part2.md) Exploring the fundamental concepts of C# types, memory management, and the Common Language Runtime's inner workings.
- [**Part IV: Advanced C# Features: Generics, Patterns, LINQ, and More:**](./part4.md) Unlocking C#'s powerful and expressive features, delving into new programming paradigms, dynamic capabilities, and low-level compiler interactions.
- [**Part V: Concurrency, Performance, and Application Lifecycle:**](./part5.md) Covering critical aspects of building and maintaining high-performance C# applications, including asynchronous programming, optimization, debugging, and deployment.
- [**Part VI: Architectural Principles and Design Patterns:**](./par6.md) Understanding the overarching principles and established design patterns for structuring robust, maintainable, and scalable C# systems.
- [**Appendix:**](./appendix.md) A collection of resources, practical checklists, and a glossary to support the learning journey.
