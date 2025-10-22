---
layout: default
title: Programming in C++ Part I | Jakub Smolik
---

[..](./index.md)

[View as PDF](./part1.pdf)

# Part I: The C++ Ecosystem and Foundation

This part introduces the foundational aspects of C++ by exploring its historical context, core philosophies, and the evolution of the language through modern standards. It covers the essential principles that distinguish C++, such as the zero-overhead abstraction, and examines the language's primary use cases, strengths, and limitations. Readers will also learn how to set up a development environment, understand the phases of program translation, and gain insight into both traditional and modern approaches to modularization, including the use of modules and the legacy preprocessor. Together, these topics provide a comprehensive overview of the C++ ecosystem and the technical groundwork necessary for effective C++ development.

## Table of Contents

#### [1. Welcome to Modern C++](#1-welcome-to-modern-c-1)

- 1.1 History, Philosophy, and The Zero-Overhead Principle
- 1.2 C++ Standards (C++17, C++20, C++23): What's Modern?
- 1.3 Key Use Cases and Advantages/Disadvantages
- 1.4 Setting up the Environment and Toolchains

#### [2. Compilation, Linking, and Modularization](#2-compilation-linking-and-modularization-1)

- 2.1 The Phases of Translation: Preprocessing, Compiling, Linking
- 2.2 **Modules:** Declaring, Exporting, and Importing Units
- 2.3 The Legacy Preprocessor: Directives and Conditional Compilation

---

## 1. Welcome to Modern C++

## 1.1 History, Philosophy, and The Zero-Overhead Principle

For an experienced C# or Java developer, the greatest challenge in learning C++ is not the syntax, but the **mindset**. Your previous languages were designed around a central principle: **Developer Productivity via Managed Runtime**. C++ is designed around an older, stricter principle: **Maximum Performance and Control via Zero-Overhead Abstraction**.

C++ began in the 1980s as "C with Classes" by Bjarne Stroustrup. Its fundamental goal was to add object-oriented features while retaining C's speed and low-level control. The philosophy that guides all C++ design decisions can be summarized in a simple rule:

> **The Zero-Overhead Principle:** You only pay for the features you use. When you use a feature, you cannot reasonably code a better alternative by hand.

This principle is the reason C++ lacks a mandated Garbage Collector (GC), a mandatory runtime check for every array access, or a Just-in-Time (JIT) compiler.

| Feature       | C#/Java Approach (Managed)                                                                     | C++ Approach (Zero-Overhead)                                                                                                   |
| :------------ | :--------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------- |
| **Memory**    | Garbage Collector (GC) runs in the background, consuming CPU and causing unpredictable pauses. | **Deterministic Resource Management (RAII).** Resources are released _immediately_ upon leaving scope, with zero runtime cost. |
| **Types**     | Strong checks are performed by the Runtime (CLR/JVM) often at JIT time, guaranteeing safety.   | Strong checks are performed **at Compile Time**. Safety features (like Smart Pointers) cost nothing when you don't use them.   |
| **Execution** | JIT compilation and Runtime environment add an initial overhead for setup and execution.       | **Static Compilation** to machine code. There is no large runtime and thus, zero startup overhead.                             |

When you use C++, you are not coding for a **Runtime**; you are coding **for the hardware**. Every C++ feature is designed to compile down to the most efficient machine code possible, giving you deterministic performance without any hidden costs or "magic."

## 1.2 C++ Standards (C++17, C++20, C++23): What's Modern?

Just as you track the evolution of C\# (e.g., C\# 8, C\# 10) or Java, it is crucial to recognize that C++ is a living, evolving language. The C++ Standardization Committee releases a major standard every three years. The era known as **Modern C++** began with C++11, which introduced features like lambdas and smart pointers.

For an experienced developer entering C++ today, the key is to focus on the standards from C++17 onward, as these represent the current state-of-the-art in production code.

### The Modern C++ Milestones

| Standard  | Status                 | Key Features for Experienced Developers                                                                                              |
| :-------- | :--------------------- | :----------------------------------------------------------------------------------------------------------------------------------- |
| **C++17** | **Adopted**            | **Structured Bindings** (deconstruction), `std::optional`, `std::variant`, `std::filesystem`, `if constexpr`.                        |
| **C++20** | **Current Baseline**   | **Modules** (Replacement for header files), **Concepts** (Template constraints), **Ranges** (Simplified STL algorithms), Coroutines. |
| **C++23** | **Latest/Near-Future** | **`std::move_only_function`**, improvements to Modules and Ranges, **deducing `this`**.                                              |

**What does "Modern C++" mean in practice?**

Modern C++ is about writing **safer, cleaner, and more expressive code** by leveraging the standard library and language features to manage complexity automatically. For example, instead of manually using **raw pointers** (Chapter 6) to manage memory, we use a **smart pointer** (Chapter 9). This smart pointer object automatically follows the **RAII** principle (Chapter 9.1), releasing the memory in its destructor.

The most significant step change is **C++20**. Two features are particularly important:

1.  **Modules:** This feature directly addresses the historical pain point of slow compilation times and header/macro complexity, which the C\# developer (used to the robust `using` system) will appreciate. Modules will be covered in detail in Chapter 2.
2.  **Concepts:** This provides a mechanism for clearly specifying the requirements of a template parameter, giving you compile-time type checking similar to C\#'s **generic constraints**, but with greater power and far clearer error messages.

## 1.3 Key Use Cases and Advantages/Disadvantages

C++ is not intended to replace languages like C\# for developing line-of-business applications, web services, or typical mobile frontends. It is a **specialization tool** used where the performance and control trade-off is unavoidable.

### Key Use Cases

- **Operating Systems and Embedded Systems:** Direct hardware access, device drivers, and environments where resources (memory, CPU cycles) are extremely limited.
- **Game Development:** High-performance rendering engines (e.g., Unreal, Unity's core), physics engines, and systems demanding ultra-low-latency processing.
- **Financial Trading and High-Frequency Systems:** Latency-critical applications where minimizing jitter and maximizing throughput require eliminating any unpredictable runtime overhead (like GC pauses).
- **High-Performance Libraries:** Components (like mathematical libraries, machine learning frameworks, or database engines) that must execute as fast as possible, often exposed to other languages (like Python, C\#, or Java) via Foreign Function Interfaces.

### Advantages and Disadvantages

| Area            | Advantage of C++                                                                                              | Disadvantage of C++ (vs. C\#)                                                                                                                |
| :-------------- | :------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------- |
| **Performance** | **Maximum Speed:** Compiles directly to machine code; fine-grained memory control.                            | **Increased Complexity:** Requires manual (or semi-manual) memory and resource management.                                                   |
| **Control**     | **Deterministic:** Complete control over memory layout, thread scheduling, and execution time (no GC jitter). | **Reduced Safety:** Manual memory management can lead to **memory leaks** and **undefined behavior** if done incorrectly.                    |
| **Ecosystem**   | **Interoperability:** Easily links with C code and C-style APIs; widely adopted standard library (STL).       | **Tooling Maturity:** Toolchains (compilers, debuggers, build systems) are often less integrated and harder to configure than those for C\#. |
| **Portability** | Excellent cross-platform capabilities (Windows, Linux, macOS, embedded systems).                              | **Longer Compile Times:** Large projects can take significantly longer to build than C\# or Java projects.                                   |

The decision to use C++ is an acceptance of higher **initial complexity** in exchange for maximum **power** and **deterministic performance**.

## 1.4 Setting up the Environment and Toolchains

In C\# development, your IDE (like Visual Studio) often abstracts the entire process: compilation, linking, and project management are all handled seamlessly by the .NET SDK and the MSBuild system.

In C++, these three steps are usually handled by **separate tools**. Understanding this separation is essential.

### 1. The Compiler

The compiler’s job is to translate C++ source code into machine code (object files). The three most common compilers are:

| Compiler                          | Platform               | Typical Use                                                                  |
| :-------------------------------- | :--------------------- | :--------------------------------------------------------------------------- |
| **GCC (GNU Compiler Collection)** | Linux, macOS, Embedded | The most common open-source standard.                                        |
| **Clang/LLVM**                    | Linux, macOS, Windows  | Highly modern, fast, and often provides the best diagnostics/error messages. |
| **MSVC (Microsoft Visual C++)**   | Windows                | The default compiler included with Visual Studio.                            |

### 2. The Build System (CMake)

Since C++ projects often involve numerous source files, external dependencies, and support for multiple platforms/compilers, a **Build System** is used to manage the complexity. We recommend learning and using **CMake** (C++ Make) as the standard, cross-platform build generator.

**CMake's Role:** CMake reads simple text configuration files (`CMakeLists.txt`) written in its own scripting language and generates **native build files** (e.g., `Makefiles` for Linux/macOS, or `.sln` and `.vcxproj` files for Visual Studio).

### Setup Quick Start (Conceptual Flow)

Assuming you have a modern IDE (like VS Code or Visual Studio) and a compiler installed:

1.  **Create your source file** (`main.cpp`):

    ```cpp
    #include <iostream>

    int main() {
        // Use the standard output stream (cout)
        std::cout << "Hello, Modern C++ Engineer!\n";
        return 0;
    }
    ```

2.  **Create a `CMakeLists.txt` file:** This instructs CMake on how to build the project.

    ```cmake
    # Minimal CMake file - C++20 standard
    cmake_minimum_required(VERSION 3.20)
    project(IntroProject LANGUAGES CXX)
    set(CMAKE_CXX_STANDARD 20)
    set(CMAKE_CXX_STANDARD_REQUIRED ON)

    # Add an executable called 'IntroProject' from main.cpp
    add_executable(IntroProject main.cpp)
    ```

3.  **Configure and Build (The Two-Step Process):**

    The typical command-line workflow (which your IDE often performs in the background) is:

    ```bash
    # Step 1: Configure - CMake generates the build files
    $ cmake -S . -B build

    # Step 2: Build - The generated files invoke the compiler
    $ cmake --build build
    ```

    This process is a fundamental difference from the single-step `dotnet build` you are accustomed to. It ensures maximum flexibility across different operating systems and compiler choices.

## Key Takeaways

- **Zero-Overhead Principle:** C++’s core philosophy is that abstractions should cost nothing unless used; this dictates the design of its performance and memory features.
- **Deterministic Management:** C++ uses **Deterministic Resource Management (RAII)** instead of a Garbage Collector (GC), giving you maximum control over when resources are released.
- **Modern C++ is C++20:** Focus on features from C++17 onward, with C++20's **Modules**, **Concepts**, and **Ranges** being crucial for writing contemporary code.
- **Target the Hardware:** C++ is the language of choice for systems and applications where low latency, direct hardware control, and maximum execution speed are non-negotiable.
- **Toolchain Separation:** The C++ development environment is characterized by the separation of the **Compiler** (e.g., Clang) and the **Build System** (e.g., CMake).

### Exercises

1.  **Identify the Trade-Off:** Compare the `try-finally` block pattern in C\# (or `try-with-resources` in Java) for ensuring a file handle is closed, to the C++ promise of RAII.

    - _Task:_ Explain conceptually how a C++ object can guarantee resource cleanup **without** a `finally` block or GC.
    - _Hint:_ The C++ mechanism relies on the **stack** and the object's **destructor** being called automatically when it goes out of scope.

2.  **Standards Research:** Look up one feature introduced in C++23 (other than those mentioned in Section 1.2) and explain in one sentence how it might improve the life of a C\# developer moving to C++.

    - _Hint:_ Consider features that improve the standard library or simplify common tasks, such as `std::expected`.

3.  **Toolchain Practice:** Set up your environment (compiler and CMake) and successfully compile and run the "Hello World" example provided in Section 1.4.
    - _Task:_ Modify the `main.cpp` to print "C++ is Fast!" and recompile.
    - _Hint:_ Use `cmake --build build` to execute the build step after the source change.

---

## 2. Compilation, Linking, and Modularization

In C\# or Java, when you run your code, the environment (the CLR or JVM) often handles the "translation" and assembly of your code invisibly. Your source files are compiled into Intermediate Language (IL) or Bytecode, and the runtime manages the final machine code generation and linking.

In C++, you operate in a **statically compiled environment**. The process of turning your source code into a runnable application is a rigid, multi-stage pipeline, requiring specific tools to manage the different steps. Understanding this pipeline—especially the role of **linking**—is foundational to mastering C++.

## 2.1 The Phases of Translation: Preprocessing, Compiling, Linking

When you invoke a C++ compiler, your source file (`.cpp`, often called a **translation unit**) goes through several distinct phases. If any phase fails, the process stops with an error.

### Phase 1: Preprocessing

The preprocessor is a simple, text-substitution tool. It executes directives, which are lines starting with a hash symbol (`#`).

- **`#include`:** This directive is the most common and dangerous. It _literally copies and pastes_ the entire contents of the named file (usually a header file) into the translation unit.
- **`#define`:** This performs simple text replacement for macros and constants.
- **Conditional Compilation:** Directives like `#ifdef` or `#if` allow sections of code to be conditionally included or excluded based on whether a macro has been defined.

**Result of Preprocessing:** A massive, merged source file (still C++ code) with all `#include`s resolved and all macros substituted. This pre-processed file is then passed to the compiler.

### Phase 2: Compiling (Translation) and Assembly

The compiler takes the pre-processed C++ code and performs the heaviest work:

1.  **Syntactic and Semantic Analysis:** It checks for language errors and ensures the code is valid C++.
2.  **Code Generation:** It translates the C++ code into platform-specific assembly language instructions.
3.  **Assembly:** The assembler (often part of the compiler suite) translates the assembly instructions into raw binary code.

**Result of Compiling:** An **Object File** (typically `.o` on Linux/macOS or `.obj` on Windows). An object file is _almost_ machine code, but it is incomplete. It contains:

- The machine code for all the functions defined in the source file.
- A list of all external symbols (functions or variables) that were _declared_ but not _defined_ (i.e., function calls that need to be resolved elsewhere).

A file that contains a definition for a function, say `void Logger::log(const std::string& message)`, compiles into machine code for that function. But if that file _calls_ a function like `std::cout`, the object file contains a placeholder indicating that the actual address for `std::cout` must be found later.

### Phase 3: Linking

The linker is the final, crucial step. It takes all the separate object files, along with necessary external libraries (like the Standard Library or OS DLLs), and performs the following:

- **Symbol Resolution:** It finds the physical memory address for every placeholder symbol in every object file. If your `main.o` calls `Logger::log()`, the linker finds `Logger::log()`'s machine code in `logger.o` and connects them.
- **Merging:** It combines all the object files and library code into a single, cohesive executable file (`.exe` or equivalent).

**The Linker Error:** If the linker cannot find the definition (the function body) for a declared symbol, it results in a dreaded "Undefined Reference" or "Unresolved External Symbol" error. This error is fundamental to C++: it means the compiler was happy (the declaration was present in the header), but the linker couldn't find the implementation (the definition in the `.cpp` file/library).

## 2.2 Modules: Declaring, Exporting, and Importing Units

The legacy `#include` system (covered in 2.3) is text substitution, which leads to slow compilation and potential **macro pollution**. C++20 introduced **Modules** as the semantic, modern replacement.

Modules allow you to organize code into logical partitions that are compiled **once** into a fast, reusable binary format, fundamentally changing how C++ scales.

### Module Terminology

| Term                           | Description                                                                                                                                        | Analogy to C\#                                                |
| :----------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| **Module Unit**                | A single C++ source file (`.cpp` or `.ixx`) that belongs to a named module.                                                                        | A single `.cs` file belonging to a defined project/namespace. |
| **Module Interface Unit**      | The primary file that defines the module's name and specifies which declarations are `export`ed for public consumption.                            | The public facing declarations of a C\# class or namespace.   |
| **Module Implementation Unit** | Source files that contain the private definitions of functions and classes declared in the interface unit. These are never visible to the outside. | Private implementation details within a C\# class/assembly.   |

### Declaring, Exporting, and Importing

To create a module, you define a **Module Interface Unit**. This file typically uses an `.ixx` extension, but the file extension is not mandatory; the key is the `export module` statement.

#### 1. Defining and Exporting (The Interface)

In the module interface file (`Geometry.ixx`):

```cpp
// Geometry.ixx - The Module Interface Unit
export module Geometry; // 1. Declares the module name

// 2. Export declarations make them public to importers
export struct Point {
    double x, y;
};

export double distance(const Point& p1, const Point& p2);

// NOTE: This function is defined below, but the definition is still exported
export void print_point(const Point& p);
```

#### 2. Defining the Implementation

In a separate file (`geometry_impl.cpp`) for implementation details:

```cpp
// geometry_impl.cpp - Module Implementation Unit
module Geometry; // 1. Declares that this belongs to the 'Geometry' module

#include <iostream>
#include <cmath>

// 2. No 'export' needed here, as the declaration was exported in the interface
double distance(const Point& p1, const Point& p2) {
    return std::sqrt(std::pow(p2.x - p1.x, 2) + std::pow(p2.y - p1.y, 2));
}

void print_point(const Point& p) {
    std::cout << "(" << p.x << ", " << p.y << ")";
}
```

#### 3. Importing and Using

In your main application file (`main.cpp`):

```cpp
// main.cpp - Consumer Code
import Geometry; // 1. Imports the module (semantic, not textual)
#include <iostream>

int main() {
    Point start {0.0, 0.0};
    Point end {3.0, 4.0};

    // Use the exported functions and types directly
    std::cout << "Start at ";
    print_point(start);
    std::cout << "\nDistance is: " << distance(start, end) << "\n";

    return 0;
}
```

### The Power of Modules

1.  **Semantic Inclusion:** `import Geometry;` is a **semantic** import. The compiler finds the pre-compiled module binary, which is much faster than re-processing a large text header file.
2.  **No Macro Pollution:** Macros defined inside a module implementation **do not leak** out to the importer. This eliminates a huge source of bugs and namespace conflicts.
3.  **Faster Compilation:** A module is compiled once, regardless of how many files import it, drastically speeding up build times in large projects.

## 2.3 The Legacy Preprocessor: Directives and Conditional Compilation

Before C++20 Modules, every declaration had to be placed in a **Header File** (`.h` or `.hpp`). The preprocessor then had to `#include` that file everywhere it was needed.

### The Problem of `#include`

Because `#include` is a simple copy-paste operation, if a header file is included multiple times within a single translation unit (e.g., File A includes B, and File C includes B and A), the contents of B will be pasted multiple times. This causes the compiler to see the same class or function declaration multiple times, resulting in a **Multiple Definition Error**.

### The Solution: Header Guards

To prevent this, every header file _must_ contain a mechanism known as **header guards**. These use conditional compilation to ensure the file's contents are processed only once per translation unit.

```cpp
// geometry.h (Legacy Header File)
// 1. Check if the unique identifier is NOT defined
#ifndef GEOMETRY_H
// 2. If not defined, define it now
#define GEOMETRY_H

// --- Contents of the Header File ---

struct Point {
    double x, y;
};

double distance(const Point& p1, const Point& p2);

// --- End of Header File Contents ---

// 3. Close the conditional block
#endif // GEOMETRY_H
```

A common, non-standard alternative supported by most major compilers is:

```cpp
// Non-standard but widely supported header guard
#pragma once
```

### Using the Preprocessor for Conditional Compilation

The preprocessor is not entirely obsolete. Its primary use remains **conditional compilation**—including or excluding code based on external definitions (like compiler flags).

```cpp
#include <iostream>

#define ENABLE_DEBUG_LOGGING // This macro can be set by a compiler flag

void perform_operation() {
    // This code only exists if ENABLE_DEBUG_LOGGING is defined
#ifdef ENABLE_DEBUG_LOGGING
    std::cout << "DEBUG: Starting resource allocation.\n";
#endif

    // ... operation logic ...
}

int main() {
    perform_operation();
    return 0;
}
```

This functionality allows C++ developers to bake platform-specific code or debug features directly into the source file that can be toggled via compiler flags, a powerful technique that operates entirely outside of the main C++ language syntax.

## Key Takeaways

- **Four Phases:** C++ compilation involves **Preprocessing** (text substitution), **Compiling** (C++ $\to$ machine code, creating object files), **Assembly**, and **Linking** (resolving symbol references).
- **Linking is Key:** If your program runs but crashes, it's often a C++ error. If your program fails to compile with an "Undefined Reference" error, it's a **Linker Error**—the definition (function body) could not be found.
- **Modules are Modern (C++20):** Use `export module` and `import` for code organization. Modules provide **semantic inclusion**, eliminate macro pollution, and significantly improve compilation speed.
- **Legacy Headers:** Old C++ code uses `#include` (textual copy-paste). Always use **Header Guards** (`#ifndef...#define...#endif` or `#pragma once`) in header files to prevent multiple definition errors.
- **Preprocessor Use:** The preprocessor (`#define`, `#ifdef`) remains necessary for conditional compilation and interacting with C-style libraries.

### Exercises

1.  **Linker Error Simulation:** Create two files: `main.cpp` and `math.cpp`. In `main.cpp`, declare a function `int add(int, int);` and call it. Do _not_ define the body of `add` in `math.cpp` or anywhere else.

    - _Task:_ Compile the project. What specific error message does the linker give you?
    - _Hint:_ The error will be "Undefined Reference" or "Unresolved External Symbol."

2.  **Header Guard Failure:** Create two header files, `A.h` and `B.h`, with no header guards. `A.h` defines a constant `const int VALUE = 10;`. `B.h` includes `A.h`. Finally, `main.cpp` includes both `A.h` and `B.h`.

    - _Task:_ Explain why this scenario leads to a compiler error (not a linker error) when compiled without header guards.
    - _Hint:_ The contents of `A.h` are pasted into `main.cpp` _twice_, leading to two definitions of `VALUE`.

3.  **Module Concept:** Explain the difference between `import MyModule;` and `#include "my_module.h"` using the terms **textual substitution** and **semantic inclusion**.

    - _Hint:_ One processes text; the other processes a pre-compiled binary interface.

4.  **Conditional Macro:** Write a simple snippet using `#ifdef` that prints a string literal only if the macro `PROD_BUILD` is _not_ defined.
    - _Hint:_ Use `#ifndef PROD_BUILD` to check if the macro is absent.

---

## Where to go Next

- **[Part I:](./part1.md): The C++ Ecosystem and Foundation:** This section establishes the philosophical and technical underpinnings of C++, focusing on compilation, linking, and the modern modularization system.
- **[Part II](./part2.md): Core Constructs, Classes, and Basic I/O:** Here, we cover the essential C++ syntax, focusing on differences in data types, scoping, **`const` correctness**, and the function of **lvalue references**.
- **[Part III](./part3.md): The C++ Memory Model and Resource Management:** The most critical section, which deeply explores raw pointers, value categories, **move semantics**, and the indispensable role of **smart pointers** and the **RAII** idiom.
- **[Part IV](./part4.md): Classical OOP, Safety, and Type Manipulation:** This part addresses familiar object-oriented concepts like **inheritance** and **polymorphism**, emphasizing C++'s rules for **exception safety** and type-safe casting.
- **[Part V](./part5.md): Genericity, Modern Idioms, and The Standard Library:** Finally, we explore the advanced capabilities of **templates**, **C++20 Concepts**, **lambda expressions**, and the power of the **Standard Library containers** and **Ranges** for highly generic and expressive code.
- **[Appendix](./appendix.md):** Supplementary materials including coding style guidelines, compiler flags, and further reading.
