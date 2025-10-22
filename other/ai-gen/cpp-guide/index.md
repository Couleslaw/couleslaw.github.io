---
layout: page
title: Programming in C++ | Jakub Smolik
---

This text was generated using Gemini (free version).

# Programming in C++

#### Purpose

This guide rapidly converts your existing expertise in managed languages (like C#/Java) into a deep, production-ready understanding of **Modern C++**. We focus on the **zero-overhead principle**, the **memory model**, and **RAII** to enable you to design and build efficient, robust, and high-performance systems.

#### Target Audience

Tailored for **experienced software engineers** proficient in OOP and generic programming from languages with Garbage Collection (C#, Java). You seek unparalleled **control** and **performance** and are ready for a pedagogical deep dive into the **"why"** and **trade-offs** unique to C++.

#### Structure

- **[Part I:](./part1.md): The C++ Ecosystem and Foundation:** This section establishes the philosophical and technical underpinnings of C++, focusing on compilation, linking, and the modern modularization system.
- **[Part II](./part2.md): Core Constructs, Classes, and Basic I/O:** Here, we cover the essential C++ syntax, focusing on differences in data types, scoping, **`const` correctness**, and the function of **lvalue references**.
- **[Part III](./part3.md): The C++ Memory Model and Resource Management:** The most critical section, which deeply explores raw pointers, value categories, **move semantics**, and the indispensable role of **smart pointers** and the **RAII** idiom.
- **[Part IV](./part4.md): Classical OOP, Safety, and Type Manipulation:** This part addresses familiar object-oriented concepts like **inheritance** and **polymorphism**, emphasizing C++'s rules for **exception safety** and type-safe casting.
- **[Part V](./part5.md): Genericity, Modern Idioms, and The Standard Library:** Finally, we explore the advanced capabilities of **templates**, **C++20 Concepts**, **lambda expressions**, and the power of the **Standard Library containers** and **Ranges** for highly generic and expressive code.
- **[Appendix](./appendix.md):** Supplementary materials including coding style guidelines, compiler flags, and further reading.

#### Learning Outcomes

Upon completing this guide, you will be able to:

- **Master Memory:** Confidently use RAII and smart pointers (`std::unique_ptr`, `std::shared_ptr`) for stack/heap management.
- **Maximize Performance:** Implement **Move Semantics** to eliminate unnecessary deep copies.
- **Enforce Safety:** Utilize **`const` correctness** and C++ casting operators for strong, compiler-checked type safety.
- **Use Modern C++:** Write clean, robust C++ using **Concepts**, **Ranges**, and advanced **lambdas** (C++17/C++20 features).
- **Employ the STL:** Effectively utilize Standard Library containers and algorithms.
- **Understand Compilation:** Explain the full translation process and use **C++ Modules** for scalable code.

## Table of Contents

### [Part I: The C++ Ecosystem and Foundation](./part1.md)

#### 1. Welcome to Modern C++

- 1.1 History, Philosophy, and The Zero-Overhead Principle
- 1.2 C++ Standards (C++17, C++20, C++23): What's Modern?
- 1.3 Key Use Cases and Advantages/Disadvantages
- 1.4 Setting up the Environment and Toolchains

#### 2. Compilation, Linking, and Modularization

- 2.1 The Phases of Translation: Preprocessing, Compiling, Linking
- 2.2 **Modules:** Declaring, Exporting, and Importing Units
- 2.3 The Legacy Preprocessor: Directives and Conditional Compilation

### [Part II: Core Constructs, Classes, and Basic I/O](./part2.md)

#### 3. Data Types and Control Flow

- 3.1 Built-in Types, Initialization, and Type Inference (`auto`)
- 3.2 Operators, Expressions, and Type Promotion
- 3.3 Scoping, Lifetimes (Automatic, Static), and Storage Duration
- 3.4 Control Structures (`if`, `switch`, loops)

#### 4. Functions, `const` Correctness, and Basic References

- 4.1 Function Overloading and Signature Matching
- 4.2 Parameter Passing: By Value and the Cost of Copying
- 4.3 **`const` Correctness:** Variables, Parameters, and Member Functions
- 4.4 **Lvalue References (Aliases)** and Passing by Reference
- 4.5 Basic Console I/O (`std::cin`, `std::cout`)

#### 5. Classes, Objects, and Data Abstraction

- 5.1 Defining a Class (Data and Member Functions)
- 5.2 Access Specifiers (`public`, `private`, `protected`)
- 5.3 Introducing Constructors and Destructors
- 5.4 Aggregate Initialization and Designated Initializers (C++20)

### [Part III: The C++ Memory Model and Resource Management (RAII)](./part3.md)

#### 6. Raw Pointers and Dynamic Allocation

- 6.1 The Stack vs. The Heap vs. The Data Segment
- 6.2 **Raw Pointers:** Declaration, Dereferencing, and the Null State
- 6.3 Manual Allocation and Deallocation (`new` and `delete`)
- 6.4 Dangers: Memory Leaks, Double Deletion, and Dangling Pointers
- 6.5 C-style Arrays, Pointer Arithmetic, and Decaying

#### 7. Value Categories and References Deep Dive

- 7.1 **Lvalues, Rvalues, and Prvalues:** Defining Object Identity
- 7.2 The Need for **Rvalue References**
- 7.3 Reference Collapsing and Forwarding References
- 7.4 The `std::move` Utility (A Cast, Not a Move)
- 7.5 The `std::forward` Utility

#### 8. Move Semantics and State Control

- 8.1 Deep vs. Shallow Copy Review
- 8.2 **The Copy Constructor** and **Copy Assignment Operator**
- 8.3 **The Move Constructor** and **Move Assignment Operator**
- 8.4 Compiler-Generated Defaults and Explicit Deletion (`= default`, `= delete`)
- 8.5 **The Rule of Zero/Three/Five:** Modern Class Design

#### 9. Smart Pointers and RAII

- 9.1 The RAII Principle: Resource Acquisition Is Initialization
- 9.2 **`std::unique_ptr`:** Exclusive, Transferable Ownership
- 9.3 **`std::make_unique`** vs. `new unique_ptr`
- 9.4 **`std::shared_ptr`:** Shared Ownership and Reference Counting
- 9.5 **`std::make_shared`** for Performance and Safety
- 9.6 **`std::weak_ptr`:** Observer Pointers and Breaking Circular References

### [Part IV: Classical OOP, Safety, and Type Manipulation](./part4.md)

#### 10. Error Handling and Exceptions

- 10.1 Throwing and Catching Exceptions
- 10.2 Creating Custom Exception Classes
- 10.3 **Exception Safety Guarantees** (Strong, Basic, Nothrow)
- 10.4 The Role of `noexcept` and `noexcept(foo)`

#### 11. Inheritance and Polymorphism

- 11.1 **Public, Protected, and Private Inheritance**
- 11.2 **Virtual Methods** and Dynamic Dispatch
- 11.3 Abstract Base Classes and Pure Virtual Functions (Interfaces)
- 11.4 The `override` and `final` Specifiers
- 11.5 **Virtual Destructors** and Deleting Polymorphic Objects
- 11.6 **Virtual Inheritance** (The Diamond Problem Solution)

#### 12. Type Conversions and Explicit Constructors

- 12.1 Implicit Type Conversions (Promotion and Conversion)
- 12.2 User-Defined Conversion Operators
- 12.3 Preventing Conversions with the **`explicit`** Keyword
- 12.4 Uniform Initialization and `std::initializer_list`

#### 13. Casting Operators and RTTI

- 13.1 C-Style Casts: Why They Are Dangerous
- 13.2 **`static_cast`**: Compile-Time Conversions
- 13.3 **`dynamic_cast`**: Run-Time Polymorphic Checking
- 13.4 **`const_cast`** and **`reinterpret_cast`**: High-Risk Operations
- 13.5 Run-Time Type Information (RTTI) and `typeid`

### [Part V: Genericity, Modern Idioms, and The Standard Library](./part5.md)

#### 14. Modern Language Constructs and Idioms

- 14.1 **Lambda Expressions** (Basic Syntax and Captures)
- 14.2 Lambda Return Types, Generic Lambdas (C++14), and `constexpr` Lambdas (C++17/20)
- 14.3 Structured Bindings and Deconstruction (C++17)
- 14.4 Compile-Time Programming with **`constexpr`**
- 14.5 `if constexpr` and Template Compilation Decisions

#### 15. Introduction to Templates and Concepts

- 15.1 Function Templates and Template Argument Deduction
- 15.2 Class Templates and Template Parameters
- 15.3 Template Specialization and Partial Specialization
- 15.4 **Concepts (C++20):** Constraining Template Parameters
- 15.5 The `requires` Keyword and Requires Clauses

#### 16. Algebraic Data Types for Robustness

- 16.1 **`std::optional`:** Handling the Absence of a Value
- 16.2 **`std::variant`:** Type-Safe Unions and `std::visit`
- 16.3 **`std::any`:** Type-Safe Polymorphic Value Container
- 16.4 Using ADTs for Modern Error Handling (vs. Exceptions)

#### 17. Standard Containers and Iterators

- 17.1 **Contiguous Containers:** `std::vector` (The Workhorse), `std::array`
- 17.2 **Sequence Containers:** `std::list`, `std::deque`
- 17.3 **Associative Containers:** `std::map`, `std::set` (Ordered)
- 17.4 **Unordered Containers:** `std::unordered_map`, `std::unordered_set`
- 17.5 Iterators: Concepts, Categories, and Range-based Operations

#### 18. The Standard Algorithms Library and Ranges (C++20)

- 18.1 The Power of `<algorithm>` and `<numeric>`
- 18.2 Common Algorithms: Search, Sort, Transform, Accumulate
- 18.3 Using Lambdas and Function Objects with Algorithms
- 18.4 Introduction to **Ranges (C++20):** Views and Adaptors
- 18.5 The Pipelining of Algorithms

#### 19. Introduction to Concurrency

- 19.1 The C++ Memory Model and Data Rac
- 19.2 The `std::thread` Basics
- 19.3 Synchronization Primitives: `std::mutex` and Locks
- 19.4 The `std::future` and `std::promise` for Asynchronous Results
- 19.5 Using Concurrency with RAII: `std::lock_guard` and `std::unique_lock`

### [Appendix](./appendix.md)

- A.1 Coding Style Guidelines (e.g., Google, LLVM)
- A.2 Common Compiler Flags and Their Uses
- A.3 Effective Use of Compiler Warnings (`-Wall`, `-Wextra`)
- A.4 Recommended Books and Online Courses
