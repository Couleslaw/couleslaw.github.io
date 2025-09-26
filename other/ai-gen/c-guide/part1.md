---
layout: default
title: C Language Basics Part I | Jakub Smolik
---

[..](./index.md)

# Part I: C Fundamentals

Part I lays the groundwork for your C journey. You'll set up your development environment, write and compile your first C program, and explore the fundamental syntax and semantics of the language. This section is designed to leverage your existing programming knowledge while introducing you to C's unique characteristics.

## Table of Contents

#### [1. Getting Started (your first C program)](#1-getting-started-your-first-c-program-1)

- **1.1. C History, Standards, and Motivation:** Understanding C's origins, its evolution through ANSI and ISO standards, and why it remains relevant today.
- **1.2. Installing a C Toolchain on Windows (MinGW/MSVC):** Step-by-step instructions for setting up a C compiler and build environment on a Windows machine.
- **1.3. Installing a C Toolchain on Linux (GCC/Clang):** Guidance on installing common C compilers and essential tools using standard Linux package managers.
- **1.4. Choosing an IDE: VS Code, CLion, Visual Studio:** A brief overview of popular integrated development environments suitable for C programming.
- **1.5. Compiling and Running "Hello, World!":** A hands-on walkthrough of writing, compiling, and executing your very first C program.
- **1.6. Basic Coding Style and Formatting:** Establishing good habits with a discussion on widely accepted C coding conventions and best practices.

#### [2. Language Primitives & Expressions (the building blocks)](#2-language-primitives--expressions-the-building-blocks-1)

- **2.1. Fundamental Integer and Floating-Point Types:** A deep dive into C's core numeric data types, including their sizes and memory representation.
- **2.2. Literals: `char`, `int`, `double`, and Suffixes:** How to correctly write constant values for various types, including the use of type-specifying suffixes.
- **2.3. Operators, Precedence, and Expressions:** Understanding C's rich set of operators, their order of evaluation, and how to combine them into expressions.
- **2.4. The Ternary Conditional Operator (`? :`):** A concise explanation of C's unique three-operand conditional operator and its common use cases.
- **2.5. Type Conversions: C# `(int)x` vs C's Type Casting:** A comparison of explicit and implicit type conversion in C to the familiar casting syntax in C#.
- **2.6. Negative Numbers: Two's Complement Representation:** An exploration of how signed integers are stored in memory using the two's complement system.

#### [3. Functions & Program Structure (organizing your code)](#3-functions--program-structure-organizing-your-code-1)

- **3.1. Function Declaration vs. Definition:** Distinguishing between a function's prototype (declaration) and its implementation (definition).
- **3.2. Header Files (`.h`) vs. Source Files (`.c`):** A guide to using header files to declare functions and source files to define them, a core concept for structuring large projects.
- **3.3. Scope and Lifetime of Local Variables:** An explanation of variable visibility and how long a variable exists during program execution.
- **3.4. The `main` Function: `argc` and `argv`:** How to access command-line arguments passed to your program through the `main` function's parameters.

---

## 1. Getting Started (your first C program)

Welcome to the world of C! As an experienced C# developer, you're used to a rich, managed environment where a lot of the low-level complexity is handled for you by the .NET runtime. This guide will help you build a new mental model for programming, one that gives you a deeper understanding of how computers and operating systems truly work. Think of this journey not as a step backward, but as a descent into the foundations of modern computing. C is the bedrock upon which many languages, including C#, were built.

## 1.1. C History, Standards, and Motivation

The C programming language was created by **Dennis Ritchie** at Bell Labs in the early 1970s. Its primary purpose was to develop the Unix operating system. This origin story is crucial, as it explains C's core philosophy: it's a systems programming language designed for direct access to hardware and memory, with minimal abstraction and a focus on efficiency.

Unlike C#, which is managed by a runtime that handles memory allocation and garbage collection, C gives you complete control. This is both its power and its primary challenge. Learning C will help you:

- **Understand low-level concepts:** Grasp what's happening behind the scenes in your C# programs, from how data is laid out in memory to how a program interacts with the operating system.
- **Write high-performance code:** C is often used for performance-critical applications, such as game engines, operating system kernels, and high-frequency trading systems, where every clock cycle matters.
- **Interface with unmanaged code:** If you've ever used **Platform Invoke (P/Invoke)** in C#, learning C will give you a fundamental understanding of the unmanaged code you're calling.

C is standardized by the **ANSI C** and later **ISO/IEC** committees. This guide will focus on modern C standards, primarily **C99** and **C11**, which introduced features like inline functions and better support for variable-length arrays. The core language has remained remarkably stable for decades.

## 1.2. Installing a C Toolchain on Windows (MinGW/MSVC)

A C# developer uses the `dotnet` CLI, which includes a compiler and runtime. A C developer needs a **toolchain**, a suite of tools including a compiler, linker, and build system. For this guide, we will use a **GCC-based toolchain**, as it is the most common compiler suite for C and cross-platform development.

For Windows, the most straightforward option is **MinGW-w64** (Minimalist GNU for Windows), a port of the GCC compiler.

1.  **Download and Install MinGW-w64:** Go to the official [MinGW-w64 download page](https://www.mingw-w64.org/doku.php/download) and download a suitable installer. The `x86_64-posix-seh` flavor is a good general-purpose choice for 64-bit systems.
2.  **Add to Path:** This is the most important step. Once installed, you need to add the `bin` directory of your MinGW installation to your system's `PATH` environment variable. This allows you to run `gcc` from any command prompt.
    - Find the installed directory (e.g., `C:\Program Files\mingw-w64\x86_64-8.1.0-posix-seh-rt_v6-rev0\mingw64\bin`).
    - Search for "Edit the system environment variables" in the Windows Start menu.
    - In the System Properties dialog, click "Environment Variables...".
    - Under "System variables," find the `Path` variable, select it, and click "Edit...".
    - Click "New" and paste the path to your `bin` directory. Click "OK" on all windows.
3.  **Verify the Installation:** Open a **new** command prompt or PowerShell window and type `gcc --version`. If the installation was successful, you'll see version information.

Alternatively, you can use the **Visual C++** toolchain provided with Visual Studio. It's fully integrated and powerful but uses a different set of command-line tools. We will use GCC commands throughout this guide for consistency.

## 1.3. Installing a C Toolchain on Linux (GCC/Clang)

Linux distributions almost always come with a C compiler pre-installed or easily accessible through their package managers. We will focus on the **GNU Compiler Collection (GCC)**.

To install GCC on a Debian/Ubuntu-based system, open a terminal and run:
`sudo apt update && sudo apt install build-essential`

The `build-essential` package installs GCC, the GNU debugger (`gdb`), and other tools necessary for compiling software.

On a Fedora/Red Hat-based system, use `dnf`:
`sudo dnf install gcc`

**Clang** is another excellent, modern C compiler often used as an alternative to GCC. You can install it with `sudo apt install clang` (Debian/Ubuntu) or `sudo dnf install clang` (Fedora). Both GCC and Clang will work for all examples in this guide.

## 1.4. Choosing an IDE: VS Code, CLion, Visual Studio

You don't need a heavy IDE to write C, but a good editor with syntax highlighting and debugging support is a huge productivity booster.

- **Visual Studio Code (VS Code):** This is a fantastic, lightweight, and highly customizable option. Install the **"C/C++" extension from Microsoft** to get IntelliSense, debugging support, and code formatting. It's an excellent choice for a C# developer used to the VS Code experience.
- **CLion:** A full-featured, commercial IDE from JetBrains. It has excellent support for C/C++ projects, integrated debugging, and smart code analysis. If you're used to the JetBrains ecosystem (e.g., Rider), this is a great choice.
- **Visual Studio:** For Windows, Visual Studio with the "Desktop development with C++" workload is a powerful choice. It offers a fully integrated development experience but can be more complex to set up for simple command-line projects.

For the purposes of this guide, we will stick to a **text editor and the command line**. This approach forces you to understand the fundamental compilation and linking process, which is the cornerstone of C development.

## 1.5. Compiling and Running "Hello, World!"

Let's write our first C program. Open your text editor and create a file named `hello.c`.

```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

Now, let's break down this tiny program.

- `#include <stdio.h>`: This is a **preprocessor directive**. In C#, you would use a `using` directive (e.g., `using System;`). Unlike `using`, `#include` tells the compiler's preprocessor to literally **copy-paste** the contents of the `stdio.h` file here. This file is a **header file** from the C Standard Library that contains the function prototype for `printf`. It's how the compiler knows that `printf` is a valid function and what arguments it expects.
- `int main()`: This is the **entry point** of every C program. The operating system starts your program by calling this function. It's analogous to C#'s `static void Main()`. The `int` return type indicates that `main` will return an integer value to the operating system.
- `printf("Hello, World!\n");`: This is a function call to `printf`, which stands for "print formatted." It's the standard C function for printing output to the console. The `\n` is an **escape sequence** that represents a newline character.
- `return 0;`: The `main` function returns an integer. By convention, a return value of `0` signals that the program **executed successfully**. Any non-zero value typically indicates an error.

Now, let's compile and run the program from your terminal or command prompt. Navigate to the directory where you saved `hello.c`.

**The Compilation Process**

The compilation process for C is a multi-step journey. Unlike C#, where the `dotnet build` command abstracts the process, you must be more explicit in C.

1.  **Preprocessing:** The preprocessor handles directives like `#include` and `#define`. It expands the source code, including the contents of header files.
2.  **Compilation:** The compiler translates the preprocessed C source code into assembly language, a low-level language specific to your computer's architecture.
3.  **Assembly:** The assembler converts the assembly code into machine code, creating an object file (`.o` or `.obj`).
4.  **Linking:** The linker combines your object file with other object files (e.g., the pre-compiled code for `printf` from the C Standard Library) to produce a single, executable file (`.exe` on Windows, or no extension on Linux/macOS).

**Running the Program**

To compile and link with GCC, type the following command:

`gcc hello.c -o hello`

- `gcc` is the compiler command.
- `hello.c` is the input source file.
- `-o hello` is a flag that tells GCC to name the output executable file `hello` (or `hello.exe` on Windows).

If you don't get any output, that's a good sign—it means the compilation was successful. Now, to run the program, type:

- On Windows: `.\hello.exe`
- On Linux/macOS: `./hello`

You should see the output: `Hello, World!`

**Understanding Compiler Flags**

For a more in-depth understanding of GCC and its myriad options, refer to the [GCC documentation](https://gcc.gnu.org/onlinedocs/). Key flags you'll frequently use include:

- `-Wall`: Enables most compiler warnings, helping you catch potential issues early.
- `-Werror`: Treats all warnings as errors, enforcing stricter code quality.
- `-g`: Includes debugging information in the compiled executable, essential for using a debugger like `gdb`.
- `-O2` or `-O3`: Enables optimization levels to improve performance, with `-O3` being more aggressive.

## 1.6. Basic Coding Style and Formatting

While C is a very flexible language, adopting a consistent coding style early on is a great habit.

- **File Naming:** C source files typically end with a `.c` extension. Header files use `.h`.
- **Function Naming:** It's a long-standing convention to use **`snake_case`** (e.g., `my_function_name`) for function and variable names, unlike C#'s `PascalCase` and `camelCase`.
- **Brace Style:** Use a consistent brace style. The most common is to place the opening brace on the same line as the function or control statement.
- **Indentation:** Use a consistent indentation style, either tabs or spaces (spaces are more common). The **Linux Kernel Coding Style** recommends 8-space tabs, but 4-space indentation is also widely used.

```c
// A good example of C coding style
#include <stdio.h> // Include standard I/O library

int calculate_sum(int a, int b) {
    // This function returns the sum of two integers.
    int sum = a + b;
    return sum;
}
```

## Key Takeaways

- **C is a low-level, compiled language** created for systems programming, giving you direct access to memory and hardware.
- **The C compilation process is explicit:** You must use a compiler like `gcc` to turn your source code into a native executable. This is a crucial difference from the C# build process.
- **`#include` is a preprocessor directive** that physically includes the contents of a header file, unlike C#'s `using` which creates a namespace alias.
- **`main` is the entry point** of your program. A `return 0;` from `main` conventionally indicates success.
- **C has a strong community convention for coding style**, including the use of `snake_case`.

### Exercises

1.  **Modify and Recompile:** Modify your `hello.c` program to print your name instead of "Hello, World!". Compile and run the new program.

    - _Hint:_ The compilation command does not need to change.

2.  **Add a Comment:** Add a comment to your `hello.c` program that explains what the `printf` function does. Compile and run it again.

    - _Hint:_ C supports both single-line (`//`) and multi-line (`/* ... */`) comments, just like C#.

3.  **Explore Compiler Warnings:** Intentionally create a compile-time error by removing the semicolon at the end of the `printf` statement. Try to compile the program. What error message does the compiler give you?
    - _Hint:_ The compiler will point you to the line and character where it encountered the error, often suggesting what it expected to see. This is your first introduction to a core C skill: **reading and interpreting compiler output.**

---

## 2. Language Primitives & Expressions (the building blocks)

In C#, you work with a well-defined set of language primitives like `int`, `long`, `double`, and `char`, all of which have a fixed size guaranteed by the .NET specification. In C, things are a bit more flexible—and consequently, more complex. This flexibility is a direct consequence of C's design for a wide range of hardware platforms. This chapter will demystify C's basic types, literals, and operators, constantly comparing them to their C# counterparts to help you build a correct mental model.

## 2.1. Fundamental Integer and Floating-Point Types

Unlike C#, where `int` is always 32 bits, the size of C's primitive types is **implementation-defined**. The C standard only guarantees a _minimum size_ and that certain types are at least as large as others. This requires you to think about the underlying hardware.

The `sizeof` operator is your most valuable tool for discovering a type's size on your current system. It returns the size in bytes.

```c
#include <stdio.h>

int main() {
    printf("Size of char:        %zu bytes\n", sizeof(char));
    printf("Size of short:       %zu bytes\n", sizeof(short));
    printf("Size of int:         %zu bytes\n", sizeof(int));
    printf("Size of long:        %zu bytes\n", sizeof(long));
    printf("Size of long long:   %zu bytes\n", sizeof(long long));
    printf("Size of float:       %zu bytes\n", sizeof(float));
    printf("Size of double:      %zu bytes\n", sizeof(double));
    printf("Size of long double: %zu bytes\n", sizeof(long double));
    return 0;
}
```

> **Note:** The `%zu` format specifier is used for `size_t` types, which is what `sizeof` returns.

The output on a typical 64-bit system might look like this:

```
Size of char:        1 bytes
Size of short:       2 bytes
Size of int:         4 bytes
Size of long:        8 bytes
Size of long long:   8 bytes
Size of float:       4 bytes
Size of double:      8 bytes
Size of long double: 16 bytes
```

#### Minimum Sizes Guaranteed by the C Standard

- `char`: At least 1 byte (8 bits)
- `short`: At least 2 bytes (16 bits)
- `int`: At least 2 bytes (16 bits)
- `long`: At least 4 bytes (32 bits)
- `long long`: At least 8 bytes (64 bits)
- `float`, `double` and `long double`: Follow the IEEE 754 standard for floating-point representation, but sizes can vary.

For more details see this wikipedia [article](https://en.wikipedia.org/wiki/C_data_types).

#### Integer Types

- `char`: A single-byte integer type, typically used to represent characters. It can be signed or unsigned (signed by default). A key difference from C# is that in C, `char` is fundamentally an **integer type**, not a dedicated character type that stores Unicode.
- `short`, `int`, `long`, `long long`: These are signed integer types. The standard guarantees `sizeof(short) <= sizeof(int) <= sizeof(long) <= sizeof(long long)`. On most modern systems, `int` is 32-bit (4 bytes).
- **Signed vs. Unsigned:** By default, integer types are `signed`, meaning they can hold positive and negative values. You can prefix them with the `unsigned` keyword (e.g., `unsigned int`). Unsigned integers can only hold non-negative values, but they can store a larger maximum positive value because the sign bit is used for magnitude.

#### Floating-Point Types

- `float`: Single-precision floating-point number.
- `double`: Double-precision floating-point number, typically the default for floating-point calculations.
- `long double`: Extended-precision floating-point number.

#### Fixed Width Integer Types

C99 introduced **fixed-width integer types** in the `<stdint.h>` header, allowing you to specify integers with exact sizes (e.g., 8, 16, 32, or 64 bits). This is especially useful for cross-platform code, binary protocols, and low-level programming where you need precise control over data size.

Common fixed-width types include:

- `int8_t`, `uint8_t`
- `int16_t`, `uint16_t`
- `int32_t`, `uint32_t`
- `int64_t`, `uint64_t`

There are also "least" and "fast" types (e.g., `int_least16_t`, `int_fast32_t`) for minimum or fastest types of at least a given width, and `intptr_t`/`uintptr_t` for pointer-sized integers.

**Example: Using fixed-width integer types**

```c
#include <stdio.h>
#include <stdint.h>

int main() {
    int8_t a = -100;
    uint16_t b = 50000;
    int32_t c = 123456789;
    uint64_t d = 1234567890123456789ULL;   // ULL means unsigned long long

    printf("int8_t a = %d\n", a);
    printf("uint16_t b = %u\n", b);
    printf("int32_t c = %d\n", c);
    printf("uint64_t d = %llu\n", d);

    return 0;
}
```

For printing these types, use the correct format specifiers (`%d`, `%u`, `%lld`, `%llu`, etc.) and consider including `<inttypes.h>` for portable macros like `PRId64`.

```c
#include <inttypes.h>

printf("Using inttypes.h macros:\n");
printf("int8_t a = %" PRId8 "\n", a);
printf("uint16_t b = %" PRIu16 "\n", b);
printf("int32_t c = %" PRId32 "\n", c);
printf("uint64_t d = %" PRIu64 "\n", d);
```

Fixed-width types help ensure your code behaves consistently across different platforms, unlike the basic types whose sizes may vary.

#### The `size_t` Type: What It Is and Why It Matters

One of the most common types you'll encounter in C, especially when working with memory, arrays, and the standard library, is `size_t`.

- **Definition:** `size_t` is an unsigned integer type defined in `<stddef.h>` and `<stdio.h>`. It is the type returned by the `sizeof` operator and is used for representing the size of objects in bytes.
- **Portability:** The actual size of `size_t` depends on the platform and compiler. On 32-bit systems, it's typically a 32-bit unsigned integer; on 64-bit systems, it's usually 64 bits. This ensures it can represent the maximum possible size of any object in memory.
- **Usage:** You'll see `size_t` as the type for parameters and return values in many standard library functions, such as `strlen`, `fread`, `malloc`, and more.

**Example: Using `size_t` with `sizeof` and `strlen`**

```c
#include <stdio.h>
#include <string.h>

int main() {
    char message[] = "Hello, C!";
    size_t length = strlen(message); // Returns the length as size_t

    printf("The message is %zu characters long.\n", length);
    printf("The size of the array is %zu bytes.\n", sizeof(message));

    return 0;
}
```

> **Note:** Use the `%zu` format specifier with `printf` to print `size_t` values portably.

**Why not just use `int` or `unsigned int`?**  
Using `size_t` ensures your code is portable and can handle the full range of possible object sizes on any platform. Mixing `size_t` with signed types can lead to compiler warnings or subtle bugs, so always use `size_t` for sizes and counts.

## 2.2. Literals: `char`, `int`, `double`, and Suffixes

A **literal** is a constant value written directly in your code. While C# has similar concepts, C's type system requires careful use of suffixes to prevent unexpected behavior.

- **Integer Literals:**
  - **Decimal:** `123`
  - **Octal:** `0123` (starts with `0`)
  - **Hexadecimal:** `0x1A` (starts with `0x`)
  - **Binary:** `0b1010` (starts with `0b`, supported in C99 and later)
- **Integer Suffixes:** By default, an integer literal is treated as an `int`. You can use suffixes to specify a different type:

  - `100U`: `unsigned int`
  - `100L`: `long int`
  - `100LL`: `long long int`
  - `100ULL`: `unsigned long long int`

- **Floating-Point Literals:**

  - By default, a floating-point literal (e.g., `3.14`) is a `double`.
  - To specify a `float`, use the `F` suffix: `3.14F`. This is important to avoid a performance penalty of converting a `double` to a `float`.
  - To specify a `long double`, use the `L` suffix: `3.14L`.

- **Character Literals:**
  - Character literals are enclosed in single quotes: `'A'`.
  - In C, a character literal is of type `int`. This is a subtle but important difference from C#. The value is the integer representation of the character in the system's character set (usually ASCII).

## 2.3. Operators, Precedence, and Expressions

C's operators are remarkably similar to those in C#, which is no surprise given that C# borrowed much of its syntax from C.

- **Arithmetic:** `+`, `-`, `*`, `/`, `%` (modulus)
- **Assignment:** `=`, `+=`, `-=`, `*=`, `/=`, `%=`
- **Increment/Decrement:** `++`, `--` (pre- and post-fix behavior is identical to C#)
- **Relational:** `==`, `!=`, `<`, `>`, `<=`, `>=`
- **Logical:** `&&` (AND), `||` (OR), `!` (NOT)

The rules for operator precedence and associativity are virtually the same as C#.

#### The `bool` Problem: Integers as Booleans

This is a critical point for C# developers. C does not have a native `bool` type until the C99 standard, and even then, it's just a macro for an integer type. In C, any non-zero integer is considered **`true`**, and `0` is considered **`false`**.

```c
#include <stdio.h>

int main() {
    int x = 5;
    int y = 0;

    if (x) { // This is true because x is non-zero
        printf("x is true\n");
    }

    if (y) { // This is false because y is zero
        printf("y is true\n");
    }

    // Logical operators return 0 or 1
    printf("!5 is %d\n", !x); // Prints 0
    printf("!0 is %d\n", !y); // Prints 1

    return 0;
}
```

The standard library header `<stdbool.h>` (part of C99) defines `bool`, `true`, and `false` as a convenience.
The size of `bool` is typically 1 byte, but it can vary by implementation.

## 2.4. The Ternary Conditional Operator (`? :`)

The ternary operator is a familiar friend from C#. It provides a concise way to write a conditional expression.

**Syntax:** `condition ? value_if_true : value_if_false;`

```c
#include <stdio.h>

int main() {
    int temperature = 25;
    const char* weather = (temperature > 20) ? "Warm" : "Cool";

    printf("The weather is %s.\n", weather); // Prints "The weather is Warm."

    return 0;
}
```

This operator works identically to its C# counterpart.

## 2.5. Type Conversions: C# `(int)x` vs C's Type Casting

Casting in C has the exact same syntax as in C#.

```c
#include <stdio.h>

int main() {
    double pi = 3.14159;
    int rounded_pi = (int)pi; // Explicit cast, just like C#

    printf("Original double: %.4f\n", pi);
    printf("Rounded int: %d\n", rounded_pi);

    return 0;
}
```

However, C also has complex **implicit conversion rules** (also known as "type promotion") that can be a source of subtle bugs. For example, when an operation involves a `float` and a `double`, the `float` is automatically promoted to a `double`. The compiler will often handle this for you, but it's crucial to be aware of what's happening.

A common pitfall is integer division:

```c
#include <stdio.h>

int main() {
    int a = 10;
    int b = 4;
    double result_incorrect = a / b; // Integer division, result is 2.000000
    double result_correct = (double)a / b; // Cast before division, result is 2.500000

    printf("Incorrect result: %f\n", result_incorrect);
    printf("Correct result: %f\n", result_correct);

    return 0;
}
```

The expression `a / b` is evaluated using integer arithmetic **before** the result is assigned to the `double`. This is a classic C bug. You must cast one of the operands to a floating-point type **before** the division occurs.

## 2.6. Negative Numbers: Two's Complement Representation

In C#, you rarely have to think about the binary representation of numbers. In C, a deeper understanding is essential. All modern computers use a system called **two's complement** to represent signed integers.

Let's use an 8-bit `char` for our example. An 8-bit number has a range of $2^8 = 256$ possible values. In a signed `char`, this range is from -128 to 127.

#### How to find the two's complement of a negative number:

1.  **Find the binary representation of the positive number.** For example, `5` is `0000 0101` in 8-bit binary.
2.  **Invert all the bits.** Change all `0`s to `1`s and `1`s to `0`s. This is the **one's complement**. `0000 0101` becomes `1111 1010`.
3.  **Add 1 to the result.** `1111 1010 + 1 = 1111 1011`.

Therefore, `1111 1011` is the two's complement representation of `-5`.

The highest bit (the leftmost one) is the **sign bit**. If it's `0`, the number is positive. If it's `1`, the number is negative. This representation makes arithmetic simple for the CPU. Integer overflow, for example, is simply a matter of the sign bit "flipping" when the number exceeds its maximum range.

## Key Takeaways

- **Type sizes are not fixed:** Use `sizeof` to determine a type's size on your specific system, as the C standard only guarantees minimum sizes.
- **`char` is an integer type:** Unlike C#, a C `char` is a single-byte integer, not a Unicode character.
- **Literals are typed:** Use suffixes like `U`, `L`, and `F` to ensure your constants are of the intended type, especially with floating-point numbers.
- **No native `bool` type:** C uses `int` for boolean logic, where `0` is false and any non-zero value is true.
- **Implicit conversions can be tricky:** Be mindful of C's promotion rules, especially in arithmetic, and use an explicit cast to force the desired behavior.
- **Two's complement is the standard:** Modern CPUs use this system to represent signed integers, which explains the behavior of negative numbers and integer overflow.

### Exercises

1.  **Size Exploration:** Write a program that prints the minimum and maximum values for `signed int`, `unsigned int`, and `long long` using the macros defined in `<limits.h>` (e.g., `INT_MAX`, `INT_MIN`). Compare these values to the ranges you'd expect from their sizes on your system.

    - _Hint:_ You'll need to `#include <limits.h>`.

2.  **Casting Challenge:** Write a program that calculates the average of two integers, `a = 5` and `b = 2`, and stores the result in a `float` variable. Demonstrate both the incorrect and correct ways to do this using type casting.

    - _Hint:_ The incorrect way will perform integer division and then convert the result. The correct way will cast one of the operands _before_ the division.

3.  **Two's Complement Proof:** Write a program that initializes a `char` variable with a value of `-1` and prints its value as an `int` using `printf("%d\n", my_char)`. Then, use bitwise operators (which will be covered in a later chapter) to print its binary representation. See if the binary output matches the two's complement representation of -1 (which is all `1`s for an 8-bit number).
    - _Hint:_ This exercise is an excellent bridge to future chapters. For the binary printing, you can loop through the bits and use a logical right shift (`>>`).

---

## 3. Functions & Program Structure (organizing your code)

In C#, the compiler and IDE handle most of the program structure for you. You don't typically need to worry about the order in which you define classes or methods within a file. C, however, requires a more deliberate approach to organizing your code, particularly with functions. Understanding the concepts of function declaration, definition, scope, and the main entry point is fundamental to writing correct and maintainable C programs.

## 3.1. Function Declaration vs. Definition

This distinction is perhaps the most important new concept for a C# developer. In C, a function must be **declared** (its prototype must be known) before it can be **called**. The function's **definition** (its implementation) can appear later. This is different from C#, where the compiler can generally find any method definition within a project, regardless of where it's located.

A **function declaration** (or **prototype**) provides the compiler with the function's signature: its name, return type, and the number and types of its parameters.

A **function definition** provides the actual body of the function.

Let's see what happens without a declaration:

```c
#include <stdio.h>

int main() {
    // This will cause a compiler error because square() has not been declared yet.
    int result = square(5);
    printf("Result: %d\n", result);
    return 0;
}

int square(int x) {
    return x * x;
}
```

When you try to compile this code, `gcc` will produce an error like "implicit declaration of function 'square'". The compiler doesn't know what `square` is when it encounters the call in `main`.

To fix this, we provide a declaration (or prototype) for `square` before `main`:

```c
#include <stdio.h>

// Function Declaration (Prototype)
int square(int x);

int main() {
    int result = square(5);
    printf("Result: %d\n", result);
    return 0;
}

// Function Definition
int square(int x) {
    return x * x;
}
```

Now the code compiles and runs correctly. The compiler sees the declaration, knows that `square` exists and what its signature is, and can then safely proceed to compile `main`. The linker will later find the actual function body and link everything together.

## 3.2. Header Files (`.h`) vs. Source Files (`.c`)

For multi-file projects, putting all function prototypes at the top of every file is unmanageable. The C solution is to use **header files** (`.h`) to centralize all function declarations. This is C's form of an API contract.

- **Header Files (`.h`):** Contain **declarations** for functions, global variables, and data structures. A header file defines the **interface** of a module.
- **Source Files (`.c`):** Contain the **definitions** (implementations) of the functions declared in the corresponding header file.

Let's refactor our `square` example into a multi-file project.

**`utils.h`** (the header file)

```c
#ifndef UTILS_H
#define UTILS_H

// Function declaration
int square(int x);

#endif
```

> **Note:** The `#ifndef`, `#define`, and `#endif` lines are **include guards**. They prevent the compiler from including the same header file multiple times in a single compilation unit, which would cause errors. This is standard practice in C.

**`utils.c`** (the source file)

```c
// We include our own header file to check for consistency
#include "utils.h"

// The function definition (implementation)
int square(int x) {
    return x * x;
}
```

**`main.c`** (our main program)

```c
#include <stdio.h>
#include "utils.h" // Include our custom header file

int main() {
    int num = 5;
    int result = square(num); // This call works because we included utils.h
    printf("The square of %d is %d.\n", num, result);
    return 0;
}
```

To compile this project, you must tell the compiler about both source files.

`gcc main.c utils.c -o app`

The compiler will compile `main.c` and `utils.c` into temporary object files, then the **linker** will combine them into a single executable named `app`.

## 3.3. Scope and Lifetime of Local Variables

- **Scope** refers to the region of the program where a variable is visible and can be accessed.
- **Lifetime** refers to the duration for which a variable exists in memory.

In C, variables have different storage durations and scopes.

1.  **Automatic Lifetime (Stack):** The default for local variables inside a function. They are created when the function is called and destroyed when the function returns. Their scope is limited to the function or block they are declared in. This is similar to C#'s value types on the stack.

2.  **Static Lifetime (Static Data Segment):** A variable declared with the `static` keyword inside a function retains its value between function calls. Its lifetime extends for the entire duration of the program, but its scope remains local to the function.

```c
#include <stdio.h>

void counter() {
    static int count = 0; // 'static' retains its value between calls
    count++;
    printf("Static count: %d\n", count);
}

void normal_counter() {
    int count = 0; // 'count' is re-initialized to 0 on every call
    count++;
    printf("Normal count: %d\n", count);
}

int main() {
    counter();        // Prints "Static count: 1"
    counter();        // Prints "Static count: 2"
    normal_counter(); // Prints "Normal count: 1"
    normal_counter(); // Prints "Normal count: 1"
    return 0;
}
```

This is a key difference from C#'s `static` keyword, which typically relates to class-level members shared across all instances. In C, `static` has multiple meanings based on context, and its use for local variables is an important pattern.

## 3.4. The `main` Function: `argc` and `argv`

In C#, your program can receive command-line arguments through the `string[] args` parameter of the `Main` method. C uses a similar, but more explicit, mechanism via the `main` function's parameters.

The full signature of `main` is: `int main(int argc, char *argv[])`

- `argc` (argument count): An integer that holds the number of command-line arguments. It is analogous to C#'s `args.Length`.
- `argv` (argument vector): An array of **C strings** (pointers to characters). It holds the arguments themselves. It's similar to C#'s `string[] args`.

The `argv` array always contains at least one element: `argv[0]`, which is the name of the executable itself. The actual arguments start at `argv[1]`.

```c
#include <stdio.h>

int main(int argc, char *argv[]) {
    printf("Number of arguments: %d\n", argc);

    printf("Program name: %s\n", argv[0]);

    if (argc > 1) {
        printf("Arguments passed:\n");
        // Loop through the arguments, starting from the second one
        for (int i = 1; i < argc; i++) {
            printf("  - %s\n", argv[i]);
        }
    }

    return 0;
}
```

> **Note:** We use the `%s` format specifier in `printf` to print a C string. We'll dive into what C strings are in the next chapter.

If you compile this program as `args_example` and run it from your terminal:

`./args_example hello world`

The output would be:

```
Number of arguments: 3
Program name: ./args_example
Arguments passed:
  - hello
  - world
```

## Key Takeaways

- **Declaration vs. Definition:** A function must be declared (its prototype known) before it is called.
- **Header and Source Files:** Header files (`.h`) contain declarations and define a module's public interface. Source files (`.c`) contain the implementation.
- **The `static` keyword:** When used on a local variable, `static` changes its lifetime from automatic (stack) to static, meaning it persists for the program's duration and retains its value between function calls.
- **Command-Line Arguments:** The `main` function receives arguments through `int argc` (count) and `char *argv[]` (an array of strings). `argv[0]` is always the program's name.

### Exercises

1.  **Multi-File Project:** Create a new project with two files: `math_utils.h` and `main.c`. In `math_utils.h`, declare a function `int add(int a, int b);`. In `main.c`, provide a definition for `add` and call it from `main` to calculate `10 + 5`. Compile and run the project.

    - _Hint:_ The compilation command will be `gcc main.c -o my_program`. You do not need a separate `.c` file for the definition.

2.  **Stateful Function:** Write a function `get_id()` that uses a `static int` to return a unique, incrementing ID on each call. The first call should return 1, the second 2, and so on. Call the function three times from `main` to prove it works.

    - _Hint:_ Initialize the `static` variable to 0 and increment it before returning its value.

3.  **Argument Counter:** Write a program that takes a variable number of command-line arguments and prints out the total number of characters in all arguments combined (excluding the program name).
    - _Hint:_ You can use a `for` loop from `i = 1` to `i < argc`. For the length of each string, you can use the standard library function `strlen` from the `<string.h>` header. The next chapter will cover this more formally, but feel free to use it now.

---

## Where to Go Next

- **[Part II](./part2.md): Mastering Memory and Pointers:** Diving deep into pointers, arrays, C strings, and dynamic memory allocation, with a focus on how these concepts differ from C#.
- **[Part III](./part3.md): Advanced Data and Operations:** Exploring structures, unions, enums, bitwise operations, and advanced type system features.
- **[Part IV](./part4.md): Practical Tooling and Resources:** Learning how to debug C programs, use common tools, and find further resources for continued learning.
