---
layout: default
title: Programming in C++ Part V | Jakub Smolik
---

[..](./index.md)

[View as PDF](./part5.pdf)

# Part V: Genericity, Modern Idioms, and The Standard Library

This part delves into the most modern and powerful features of C++. Chapters 14 through 19 introduce advanced language constructs such as lambda expressions, structured bindings, and compile-time programming with `constexpr`. You will learn the fundamentals of generic programming with templates and concepts, and how algebraic data types like `std::optional`, `std::variant`, and `std::any` can improve code robustness and error handling. The section also covers the rich ecosystem of standard containers and iterators, explores the extensive algorithms library and the new Ranges framework introduced in C++20, and provides a practical introduction to concurrency using threads, synchronization primitives, and asynchronous programming tools. Together, these chapters equip you to write expressive, efficient, and safe modern C++ code.

## Table of Contents

#### [14. Modern Language Constructs and Idioms](#14-modern-language-constructs-and-idioms-1)

- 14.1 **Lambda Expressions** (Basic Syntax and Captures)
- 14.2 Lambda Return Types, Generic Lambdas (C++14), and `constexpr` Lambdas (C++17/20)
- 14.3 Structured Bindings and Deconstruction (C++17)
- 14.4 Compile-Time Programming with **`constexpr`**
- 14.5 `if constexpr` and Template Compilation Decisions

#### [15. Introduction to Templates and Concepts](#15-introduction-to-templates-and-concepts-1)

- 15.1 Function Templates and Template Argument Deduction
- 15.2 Class Templates and Template Parameters
- 15.3 Template Specialization and Partial Specialization
- 15.4 **Concepts (C++20):** Constraining Template Parameters
- 15.5 The `requires` Keyword and Requires Clauses

#### [16. Algebraic Data Types for Robustness](#16-algebraic-data-types-for-robustness-1)

- 16.1 **`std::optional`:** Handling the Absence of a Value
- 16.2 **`std::variant`:** Type-Safe Unions and `std::visit`
- 16.3 **`std::any`:** Type-Safe Polymorphic Value Container
- 16.4 Using ADTs for Modern Error Handling (vs. Exceptions)

#### [17. Standard Containers and Iterators](#17-standard-containers-and-iterators-1)

- 17.1 **Contiguous Containers:** `std::vector` (The Workhorse), `std::array`
- 17.2 **Sequence Containers:** `std::list`, `std::deque`
- 17.3 **Associative Containers:** `std::map`, `std::set` (Ordered)
- 17.4 **Unordered Containers:** `std::unordered_map`, `std::unordered_set`
- 17.5 Iterators: Concepts, Categories, and Range-based Operations

#### [18. The Standard Algorithms Library and Ranges (C++20)](#18-the-standard-algorithms-library-and-ranges-c20-1)

- 18.1 The Power of `<algorithm>` and `<numeric>`
- 18.2 Common Algorithms: Search, Sort, Transform, Accumulate
- 18.3 Using Lambdas and Function Objects with Algorithms
- 18.4 Introduction to **Ranges (C++20):** Views and Adaptors
- 18.5 The Pipelining of Algorithms

#### [19. Introduction to Concurrency](#19-introduction-to-concurrency-1)

- 19.1 The C++ Memory Model and Data Rac
- 19.2 The `std::thread` Basics
- 19.3 Synchronization Primitives: `std::mutex` and Locks
- 19.4 The `std::future` and `std::promise` for Asynchronous Results
- 19.5 Using Concurrency with RAII: `std::lock_guard` and `std::unique_lock`

---

## 14. Modern Language Constructs and Idioms

Modern C++ development relies heavily on several features introduced in C++11 and later (C++14, C++17, C++20). These features dramatically improve expressiveness, reduce boilerplate, and push computation from runtime into the compile-time phase, leading to highly optimized binaries.

## 14.1 Lambda Expressions (Basic Syntax and Captures)

**Lambda expressions** are inline, anonymous functions, similar to those found in C#. They are commonly used as function objects (functors) with the Standard Algorithms Library (Chapter 18) and for defining local behavior.

The core syntax is composed of three parts: **capture clause**, **parameter list**, and **body**.

```cpp
[capture_clause](parameters) -> return_type {
    // body
}
```

### The Capture Clause (`[]`)

The **capture clause** is the most unique and critical part of C++ lambdas, defining how the lambda accesses variables from the enclosing scope. Unlike C\# closures, C++ requires explicit specification of every external variable used, controlling the variable's lifetime and mutability.

| Capture Syntax | Mechanism                         | Lifetime/Mutability                                                                                   | Analogy                                                |
| :------------- | :-------------------------------- | :---------------------------------------------------------------------------------------------------- | :----------------------------------------------------- |
| **`[x]`**      | **Capture by Value**              | A private, immutable copy of `x` is made inside the lambda object upon creation.                      | Read-only copy of a local variable.                    |
| **`[&x]`**     | **Capture by Reference**          | The lambda holds a reference to the original variable `x`. The variable is mutable within the lambda. | Standard C\# closure capture (be careful of lifetime). |
| **`[=]`**      | **Implicit Capture by Value**     | Captures all used variables by value.                                                                 | Avoided in large scopes.                               |
| **`[&]`**      | **Implicit Capture by Reference** | Captures all used variables by reference.                                                             | Dangerous; highly discouraged due to lifetime issues.  |
| **`[this]`**   | **Capture by Pointer/Copy**       | Captures the pointer to the enclosing class instance.                                                 | C\# instance methods.                                  |

**Example of Captures:**

```cpp
int x = 10;
int y = 5;

// Capture x by value (copy) and y by reference
auto my_lambda = [x, &y]() {
    // x_copy is 10. Cannot change x_copy unless the lambda is 'mutable'.
    std::cout << "x (value): " << x << "\n";

    // y is a reference to the original y. Changes here affect the outside y.
    y += 100;
};

my_lambda();
std::cout << "External y is now: " << y << "\n"; // Output: 105
```

### Lifetime Warning: The Danger of Reference Capture

Capturing a variable by reference (`[&x]` or `[&]`) poses a severe risk if the lambda object outlives the captured variable.

If the lambda is stored or returned and later executed, and the local variable `x` has already gone out of scope (its memory is invalid), the lambda will access a **dangling reference** leading to **Undefined Behavior**. This is a major concern in C++ that C\# developers rarely encounter due to garbage collection extending the lifetime of captured variables.

## 14.2 Lambda Return Types, Generic Lambdas, and `constexpr` Lambdas

### Return Types

If a lambda body consists of a single `return` statement, the return type is automatically deduced (similar to C\#). Otherwise, you must use a **trailing return type** (`-> Type`):

```cpp
// Deduced return type: int
auto single_stmt = [] (int a) { return a * 2; };

// Trailing return type: required for complex bodies
auto multi_stmt = [] (int a) -> int {
    if (a < 0) { return 0; }
    return a * 2;
};
```

### Generic Lambdas (C++14)

Generic lambdas allow you to use the `auto` keyword in the parameter list. This effectively makes the lambda a **template** where the compiler deduces the types of the parameters when the lambda is called.

```cpp
// The compiler treats 'T' as a template type parameter for the call operator
auto generic_adder = [](auto a, auto b) {
    return a + b;
};

int i = generic_adder(5, 10);     // T is int
double d = generic_adder(5.5, 1.2); // T is double
```

Generic lambdas greatly simplify writing functional code that works with any compatible type, eliminating the need for complex template syntax in many cases.

### `constexpr` Lambdas (C++17/20)

Since C++17, lambdas can be marked **`constexpr`**, allowing their evaluation to potentially happen entirely at compile time (Section 14.4). This is a powerful optimization, especially when the lambda is used in the initialization of a `constexpr` variable.

## 14.3 Structured Bindings and Deconstruction (C++17)

**Structured Bindings** provide a concise syntax for unpacking the elements of an aggregate object (like a tuple, pair, array, or struct) into named variables. This is conceptually similar to C\#'s deconstruction of tuples.

The syntax uses `auto` followed by a list of names enclosed in square brackets: `auto [name1, name2, ...] = expression;`.

### Unpacking `std::pair` and `std::map`

Structured bindings are most frequently used to handle return values from standard containers, such as iterating over a map's key-value pairs or handling the result of an insertion operation (`std::pair<iterator, bool>`).

```cpp
#include <map>
#include <string>

std::map<int, std::string> users = {
    {1, "Alice"}, {2, "Bob"}
};

// Cleanly unpacks the key and value from the map's std::pair
for (const auto& [id, name] : users) {
    std::cout << "User " << id << " is " << name << "\n";
}

// Example: Unpacking a return pair from std::map::insert
auto result = users.insert({3, "Charlie"});

// result is a std::pair<std::map<...>::iterator, bool>
// iterator_pos gets the iterator, was_inserted gets the bool status
const auto& [iterator_pos, was_inserted] = result;

if (was_inserted) {
    std::cout << "Inserted new user: " << iterator_pos->second << "\n";
}
```

## 14.4 Compile-Time Programming with `constexpr`

The **`constexpr`** keyword is a cornerstone of performance optimization in Modern C++. It stands for **"constant expression"** and is a request to the compiler to evaluate the expression, variable, or function at **compile time**.

### `constexpr` Variables

When applied to a variable, `constexpr` requires that the variable be initialized by a value known during compilation.

```cpp
// 1. const means read-only at runtime
const int run_time_val = get_value(); // Calculated at runtime

// 2. constexpr means evaluated at compile time
constexpr int compile_time_val = 10 + 20; // Replaced by 30 in the code

// 3. constexpr implies const
constexpr int answer = 42;
// answer is immutable (const), and its value is known at compile time.
```

### `constexpr` Functions

When applied to a function, `constexpr` means the function **can** be evaluated at compile time if all its arguments are also compile-time constants. If the arguments are not constant, the function degrades gracefully and is evaluated at run time.

```cpp
// Function that can run at compile time
constexpr int power(int base, int exp) {
    // Only simple, non-side-effecting logic is allowed in constexpr functions (rules relaxed in C++14/17)
    int res = 1;
    for (int i = 0; i < exp; ++i) {
        res *= base;
    }
    return res;
}

int runtime_input = 2;

// Evaluated at compile time; compiler substitutes '1024'
constexpr int result1 = power(2, 10);

// Evaluated at run time because the argument is not constant
int result2 = power(runtime_input, 5);
```

The goal of `constexpr` is **zero-overhead abstraction**: you write readable, type-safe functions, but the performance cost is zero because the calculation is finished before the user ever runs the program.

## 14.5 `if constexpr` and Template Compilation Decisions

The **`if constexpr`** conditional statement (C++17) is used exclusively inside templates or generic lambdas to make decisions about code compilation at **compile time**.

### The Problem with Runtime `if` in Templates

In generic C++ code (templates), a standard `if` statement evaluates at runtime, but the compiler must **still compile both branches** of the `if`. If one branch contains code that is syntactically invalid for a specific template type, the entire compilation fails, even if that branch would never be executed at runtime.

### The Solution: `if constexpr`

`if constexpr` forces the condition to be evaluated at compile time. Critically, if the condition is false, the compiler **discards the false branch entirely** before compilation and type checking.

```cpp
template <typename T>
void print_value(const T& value) {
    // If T is a pointer type...
    if constexpr (std::is_pointer_v<T>) {
        // This branch is only compiled if T is actually a pointer.
        std::cout << "Pointer value: " << *value << "\n";
    } else {
        // This branch is only compiled if T is NOT a pointer.
        std::cout << "Direct value: " << value << "\n";
    }

    // Example: If T is 'int', the compiler only compiles the 'else' branch.
    // The code in the 'if' branch (dereferencing a non-pointer) is never checked.
}
```

`if constexpr` is essential for writing robust, efficient generic functions that need to choose entirely different implementation strategies based on the characteristics of the types they operate on (a form of **tag dispatch**).

## Key Takeaways

- **Lambda Capture is Explicit:** C++ lambdas require explicit capture (`[]`). Use capture by **value** (`[x]`) for safety unless you explicitly need mutation and are certain of the object's lifetime. Avoid implicit reference capture (`[&]`).
- **Generic Lambdas:** Use `auto` in the parameter list (`[](auto x)`) to create simple, inline templates without complex syntax.
- **Structured Bindings:** Use `auto [a, b] = ...` to cleanly deconstruct tuples, pairs, and struct members, improving code readability, especially when iterating over maps.
- **`constexpr` for Performance:** The **`constexpr`** keyword requests compile-time evaluation, shifting computation from runtime to build time for zero-overhead performance gains. It implies `const`.
- **`if constexpr` for Generics:** Use **`if constexpr`** inside templates to make compile-time decisions, discarding entire branches of code that are inappropriate for a specific type, thereby solving template compilation issues.

### Exercises

1.  **Capture and Lifetime:** Write a function that creates an `int` on the stack and then returns a lambda that captures the `int` by **reference** (`[&]`). In `main`, call the function and immediately execute the returned lambda.

    - _Task:_ Observe the runtime error or garbage output. Explain why this demonstrates the core danger of reference capture in C++.
    - _Hint:_ The `int` goes out of scope when the function returns, leaving the lambda with a dangling reference.

2.  **Generic Lambda vs. Function:** Write a single generic lambda function using `auto` that accepts one argument and returns the square of that argument.

    - _Task:_ Call the lambda with an `int` and a `double` to verify it works for both types without requiring explicit template syntax.
    - _Hint:_ The `auto` keyword in the parameter list creates a function call operator template within the lambda's closure type.

3.  **Structured Binding with Map:** Create a `std::map<std::string, int>` of employee names and IDs.

    - _Task:_ Use a structured binding in a `for` loop (`for (const auto& [name, id] : employees)`) to print the contents of the map.
    - _Hint:_ The element of the map is a `std::pair<const std::string, int>`.

4.  **`constexpr` vs. `const`:** Write two functions: one `int calculate_runtime(int a, int b)` and one `constexpr int calculate_compiletime(int a, int b)`.

    - _Task:_ Initialize two variables: `constexpr int result_c = calculate_compiletime(5, 5);` and `const int result_r = calculate_compiletime(5, 5);`. Then, try to initialize a third: `constexpr int result_f = calculate_compiletime(runtime_variable, 5);` where `runtime_variable` is read from user input. Explain why the last initialization fails and why the middle one works.
    - _Hint:_ `constexpr` is a contract; it fails compilation if the inputs aren't constants. `const` only guarantees immutability at runtime.

5.  **`if constexpr` Utility:** Write a generic function template `void print_size(T value)` that uses **`if constexpr`** to check if `T` is a standard container (e.g., using `std::is_same_v<T, std::vector<int>>`).
    - _Task:_ If it is a container, print `value.size()`. If it is not, print "Not a container." Demonstrate that the compiler correctly handles calling `size()` only when appropriate.
    - _Hint:_ If you tried this with a regular `if`, the code would fail to compile for a type `T` that doesn't have a `.size()` member.

---

## 15. Introduction to Templates and Concepts

**Templates** are the foundation of C++'s generic programming paradigm, serving the same role as generics in C# but operating entirely at **compile time**. C++ templates are often described as **compile-time code generation**: the compiler effectively writes and compiles a new version of the function or class for every unique set of types used.

Since C++20, **Concepts** provide a powerful, necessary way to define and enforce the requirements that template arguments must meet, finally solving a long-standing problem of cryptic template error messages.

## 15.1 Function Templates and Template Argument Deduction

A **Function Template** defines a family of functions where the type of one or more arguments is left generic.

### Syntax and Argument Deduction

Function templates begin with `template <typename T>` (or `template <class T>`). The keyword `typename` is simply a convention here, indicating that `T` is a type parameter.

```cpp
#include <iostream>

// T is the generic type parameter
template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    // The compiler automatically deduces T is 'int'
    int i = add(5, 10);

    // The compiler automatically deduces T is 'double'
    double d = add(5.5, 1.2);

    // ERROR: T cannot be deduced to both int and double
    // auto mixed = add(5, 1.2);

    // Explicitly specify the template argument (forces conversion)
    auto explicit_d = add<double>(5, 1.2); // T is double; 5 is converted to 5.0

    return 0;
}
```

**Template Argument Deduction** is a powerful feature: the compiler examines the arguments passed to the function and automatically figures out what the template parameter `T` should be.

## 15.2 Class Templates and Template Parameters

**Class Templates** define generic classes (like `std::vector<T>` or `std::shared_ptr<T>`).

### Type Parameters

Type parameters are declared using `typename` or `class`.

```cpp
template <typename T>
class MyContainer {
private:
    T value_;
public:
    MyContainer(T val) : value_(val) {}
    T get() const { return value_; }
};

int main() {
    // Explicitly specify the template argument T
    MyContainer<int> int_c(42);

    // C++17 Class Template Argument Deduction (CTAD) allows this simpler syntax
    // MyContainer c2(3.14); // Compiler deduces T is double

    return 0;
}
```

### Non-Type Template Parameters

Templates can also be parameterized by non-type parameters, which must be compile-time constants (e.g., integers, booleans, pointers, or lvalue references). This is commonly used to specify the size of a container, as with `std::array<T, N>`.

```cpp
// N is a non-type template parameter (an integer size)
template <typename T, int N>
class StaticArray {
private:
    T data_[N];
public:
    int size() const { return N; }
    // ...
};

// The size N must be provided explicitly as a compile-time constant
StaticArray<double, 10> buffer;
```

## 15.3 Template Specialization and Partial Specialization

Template specialization allows you to provide a custom implementation for a template when it is instantiated with specific types. This is necessary when the generic algorithm is inefficient or incorrect for certain types.

### Full Specialization

**Full Specialization** provides a complete, custom implementation for a specific template type (e.g., handling the generic `add` function specifically for `const char*`).

The syntax starts with an empty template list `template <>` followed by the specialized type:

```cpp
// Generic implementation (handles all types)
template <typename T> T max_val(T a, T b) { return a > b ? a : b; }

// Full specialization for const char* (needs strcmp)
template <>
const char* max_val<const char*>(const char* a, const char* b) {
    // The generic implementation would just compare the memory addresses!
    return (std::strcmp(a, b) > 0) ? a : b;
}
```

### Partial Specialization

**Partial Specialization** applies only to **class templates** (not function templates). It provides a custom implementation for a subset of the template parameters or when the parameters meet certain conditions (e.g., specializing for pointers, but not a specific pointer type).

```cpp
// Generic class template (primary template)
template <typename T, typename U>
class PairWrapper { /* generic implementation */ };

// Partial specialization: Custom implementation where the second type is a pointer
template <typename T, typename U>
class PairWrapper<T, U*> {
    // Specialized implementation for when U is a pointer type (U*)
    // e.g., to handle resource management for the pointer
};
```

## 15.4 Concepts (C++20): Constraining Template Parameters

Before C++20, if a template function was instantiated with a type that didn't meet its requirements (e.g., calling `T::size()` when `T` didn't have a `size()` method), the resulting compile error was verbose, obtuse, and pointed deep inside the template code. This was known as **SFINAE error messages** (Substitution Failure Is Not An Error).

**Concepts** solve this problem by providing a clear, compile-time contract. A Concept is a named boolean expression that specifies the requirements (methods, operators, traits) a type must satisfy.

### Defining a Concept

A concept is defined using the `concept` keyword:

```cpp
#include <concepts>

// A concept defining that a type T must support the '<' operator
template <typename T>
concept LessThanComparable = requires (T a, T b) {
    { a < b } -> std::same_as<bool>; // Requires that 'a < b' is a valid expression that returns bool
};

// A concept combining existing requirements
template <typename T>
concept Sortable = std::default_initializable<T> && LessThanComparable<T>;
```

### Using Concepts (The Terse C++20 Syntax)

Concepts are used directly in the template parameter list, replacing the generic `typename T`. This makes the code much clearer.

```cpp
// Instead of: template <typename T>
template <LessThanComparable T>
T min_val(T a, T b) {
    // Compiler only accepts types T that satisfy the LessThanComparable concept
    return a < b ? a : b;
}

// Terse syntax for concepts:
// template <std::integral T> // T must be an integral type (int, long, etc.)
// template <std::container T> // T must be a standard container
```

If you call `min_val` with a custom type that doesn't define `operator<`, the compiler error is simple and clear: "Error: Type X does not satisfy the LessThanComparable concept."

## 15.5 The `requires` Keyword and Requires Clauses

The **`requires`** keyword is the underlying mechanism used to check and specify type properties for concepts. It can be used in two ways:

### 1. Simple `requires` Clause (For Concept Definition)

Used within a concept definition (as shown above) to check for a combination of syntax validity, return type, and other conditions.

### 2. Constraints on Function Templates

A `requires` clause can be placed immediately after the function signature to specify the constraints directly, without defining a separate, named concept. This is often used for complex, one-off constraints.

```cpp
// T must be callable with two int arguments AND return void.
template <typename T>
void execute_callback(T func)
requires requires (T f) {
    { f(1, 2) } -> std::same_as<void>;
}
{
    func(10, 20);
}
```

While powerful, the syntax is more verbose. In Modern C++, the preferred method is to define a clear, named **Concept** (Section 15.4) and use the terse syntax.

## Key Takeaways

- **Templates are Compile-Time Generics:** C++ templates are powerful tools for code generation, creating specialized code for each unique type used.
- **Argument Deduction:** Function templates often allow the compiler to automatically deduce the type parameters, simplifying the call site. Class templates typically require explicit type specification.
- **Specialization for Exceptions:** Use **Full Specialization** for function templates and **Partial Specialization** for class templates to provide custom implementations when the generic solution is inadequate for a specific type or class of types (e.g., all pointer types).
- **Concepts are Contracts:** **Concepts** (C++20) are compile-time predicates that clearly define the required interface or properties of a type used in a template.
- **Cleaner Errors:** The primary benefit of Concepts is replacing confusing, verbose template errors with clear, concise messages stating exactly which requirement the type failed to satisfy.
- **Terse Syntax:** The preferred C++20 way to use concepts is to substitute the concept name for `typename T` in the template parameter list (e.g., `template <Sortable T>`).

### Exercises

1.  **Function Template Deduction:** Write a function template `auto multiply(T a, U b)` that accepts two different generic types, `T` and `U`.

    - _Task:_ Call the function with `multiply(5, 2.5)`. Observe the return type deduction. Then, try to enforce an explicit return type of `double` using the trailing return syntax (`-> double`).
    - _Hint:_ The return type of `5 * 2.5` will default to `double`.

2.  **Class Template Specialization:** Create a primary class template `Printer<T>` that prints the value of `T`.

    - _Task:_ Create a **full specialization** `Printer<const char*>` that prints `"String literal: "` before the value. Demonstrate that the specialized version is called when you instantiate `Printer<const char*>` and the generic version is called for `Printer<int>`.
    - _Hint:_ The specialization must begin with `template <>` and include the fully specified type in the class name.

3.  **Concept Creation and Usage:** Define a simple concept called `HasId` that requires the type `T` to have a public member function `int get_id() const`.

    - _Task:_ Write a function template `print_id` that takes a type constrained by `HasId` and calls `get_id()`. Test the function with a simple struct that implements `get_id()` and another struct that does not.
    - _Hint:_ The concept definition should use the `requires` keyword, checking the validity of the expression `{ t.get_id() } -> std::same_as<int>;`.

4.  **Non-Type Template Parameter Utility:** Write a class template `Buffer<T, N>` that uses a non-type template parameter `N` to statically allocate a `std::array<T, N>` as its internal storage.
    - _Task:_ Instantiate two buffers with different sizes: `Buffer<int, 5>` and `Buffer<double, 100>`. Show that the size of the buffer is determined at compile time.
    - _Hint:_ `N` must be used directly as the size argument in the internal array declaration.

---

## 16. Algebraic Data Types for Robustness

**Algebraic Data Types (ADTs)** are composite types used in functional programming to model data structure boundaries and states explicitly. In C++, these are implemented via the standard library types `std::optional`, `std::variant`, and `std::any`, introduced in C++17. These types dramatically improve safety and code clarity by forcing the programmer to handle specific conditions (like "the absence of a value" or "one of several possible types") at the compile stage.

## 16.1 `std::optional`: Handling the Absence of a Value

`std::optional<T>` is a type that either holds a value of type `T` or holds **no value** (represented by the placeholder `std::nullopt`). It is the modern, type-safe replacement for using magic values (like $-1$) or raw pointers/references that might be `nullptr` to signify "missing data."

### Characteristics

- **Type Safety:** It explicitly separates the "missing" state from the value state. Unlike a raw pointer, you must explicitly check the optional before attempting to access the value.
- **Zero Overhead (Usually):** `std::optional` is typically implemented to require only one byte of extra space (a boolean flag) beyond the storage required for type `T`.

### Usage

```cpp
#include <optional>
#include <iostream>

std::optional<int> safe_division(int a, int b) {
    if (b == 0) {
        return std::nullopt; // Return "no value"
    }
    return a / b; // Return the calculated value
}

void process_result() {
    auto result = safe_division(10, 2);

    // 1. Safe checking (similar to C#'s if (value.HasValue))
    if (result) { // Implicit conversion to bool checks for presence
        // 2. Accessing the value (requires check)
        std::cout << "Result is: " << result.value() << "\n";
    } else {
        std::cout << "Division failed (no value).\n";
        // result.value() here would throw std::bad_optional_access
    }

    // Modern C++: Get the value or provide a default (similar to C#'s ?? operator)
    int final_value = safe_division(10, 0).value_or(-1);
    std::cout << "Default value: " << final_value << "\n";
}
```

## 16.2 `std::variant`: Type-Safe Unions and `std::visit`

`std::variant<Types...>` is a class template that safely holds a value of **one** type from a predefined set of types at any given time. It is C++'s **discriminated union**—the compiler knows the finite set of types the variant might hold, ensuring type safety.

### Characteristics

- **Fixed Types (Closed Set):** The types in the template list (`T1, T2, T3`) are the _only_ types it can ever hold.
- **Type-Safe:** Unlike C-style unions, `std::variant` keeps track of which type it currently holds (its "state"), preventing you from accessing the wrong type.
- **Zero Overhead (Usually):** No dynamic memory allocation is involved; memory is allocated on the stack to fit the _largest_ of the allowed types plus a small index to track the current type.

### Usage

```cpp
#include <variant>
#include <string>
#include <iostream>

// The variant can hold an int, a double, or a std::string
using Data = std::variant<int, double, std::string>;

void handle_data() {
    Data d1 = 42; // Currently holds int
    Data d2 = "hello"; // Currently holds std::string

    // 1. Unsafe check (similar to pattern matching 'is' operator - prefer std::visit)
    if (std::holds_alternative<int>(d1)) {
        // Safe access via std::get<Type>
        std::cout << "Int value: " << std::get<int>(d1) << "\n";
    }

    // 2. Set d1 to a different type
    d1 = 3.14159; // Now holds double
    // std::cout << std::get<int>(d1); // Throws std::bad_variant_access!
}
```

### The Safest Access: `std::visit`

The best way to operate on a `std::variant` is using **`std::visit`**. This function takes a **visitor** (usually a lambda or functor) and the variant(s). The visitor must be callable with _all_ possible types held by the variant, ensuring you handle every state—a guarantee checked at **compile time**.

```cpp
auto visitor = [](auto&& arg) {
    using T = std::decay_t<decltype(arg)>; // Get the actual type T
    if constexpr (std::is_same_v<T, int>) {
        std::cout << "Variant holds int: " << arg * 2 << "\n";
    } else if constexpr (std::is_same_v<T, double>) {
        std::cout << "Variant holds double: " << arg / 2.0 << "\n";
    } else {
        std::cout << "Variant holds string (or unknown type).\n";
    }
};

Data d3 = 100;
std::visit(visitor, d3); // Calls the int branch
d3 = 50.0;
std::visit(visitor, d3); // Calls the double branch
```

## 16.3 `std::any`: Type-Safe Polymorphic Value Container

**`std::any`** is designed to hold a value of **any** type that is copy-constructible. It is similar to C\#'s **`object`** type but requires explicit casting for safety.

### Characteristics

- **Heterogeneous Types (Open Set):** Unlike `std::variant`, the set of possible contained types is **not** fixed at compile time.
- **Run-time Overhead:** `std::any` usually allocates memory on the heap (unless the contained type is very small) and stores type information (**RTTI**), incurring run-time cost.

### Usage

To retrieve the value, you must use **`std::any_cast<T>()`**, which performs a run-time type check.

```cpp
#include <any>

void handle_any() {
    std::any a;
    a = 42;
    a = std::string("Hello World"); // Type changes at runtime

    try {
        // Safe retrieval: throws std::bad_any_cast if types don't match
        std::string s = std::any_cast<std::string>(a);
        std::cout << "Contained string: " << s << "\n";

        // int i = std::any_cast<int>(a); // Throws std::bad_any_cast
    } catch (const std::bad_any_cast& e) {
        std::cerr << "Cast failed: " << e.what() << "\n";
    }
}
```

**Best Practice:** `std::any` should be used sparingly, typically only when dealing with dynamic configuration, reflection-like contexts, or parameter passing where the type cannot be known until runtime. Prefer `std::variant` when the set of types is closed.

## 16.4 Using ADTs for Modern Error Handling (vs. Exceptions)

While exceptions are mandatory for catastrophic errors (Chapter 10), modern C++ (following functional programming trends) prefers using ADTs for **expected, recoverable errors**.

This idiom is often implemented using a conceptual **`Result<T, E>`** type, which is effectively a **`std::variant<T, E>`** where `T` is the expected success value and `E` is an error type (like a simple error code or message string).

### The C++ Error-Handling ADT (The `std::expected` Idiom)

The standard library adopted this with **`std::expected`** in C++23, but the idiom is achievable with `std::variant`:

```cpp
// Conceptual type for recoverable errors (Success or Failure)
using NetworkResult = std::variant<std::string, int /*Error Code*/>;

NetworkResult fetch_data(int id) {
    if (id < 0) {
        // Return an error (int)
        return -404;
    }
    // Return the success value (std::string)
    return "Data for user " + std::to_string(id);
}

void process_network() {
    auto result = fetch_data(-1);

    // Explicitly handle all outcomes using std::visit
    std::visit([](auto&& arg) {
        using T = std::decay_t<decltype(arg)>;
        if constexpr (std::is_same_v<T, std::string>) {
            // Success branch
            std::cout << "Data received: " << arg << "\n";
        } else {
            // Error branch (T is int)
            std::cerr << "Network error code: " << arg << "\n";
        }
    }, result);
}
```

This approach forces the caller to explicitly check and handle the error, leading to more robust code, avoiding the non-local control flow and performance costs associated with exceptions.

## Key Takeaways

- **`std::optional` for Absence:** Use **`std::optional<T>`** to explicitly model the possibility of a missing value, replacing ambiguous `nullptr`s or magic return values. Check presence with `if (opt)` or use `.value_or()`.
- **`std::variant` for Closed Sets:** Use **`std::variant<T1, T2, ...>`** for type-safe unions where the set of possible types is known and fixed at compile time. It is a powerful form of static polymorphism.
- **`std::visit` is Safest:** Access `std::variant` contents using **`std::visit`** with a lambda/functor that handles _every_ possible type, ensuring exhaustive handling.
- **`std::any` for Open Sets:** Use **`std::any`** only when the contained type is truly unknown at compile time (like C\#'s `object`), but be aware of the run-time overhead and the need for **`std::any_cast`**.
- **ADTs for Error Handling:** Modern C++ uses ADTs (like the `std::expected` idiom, often implemented with `std::variant`) to handle recoverable errors explicitly, returning them as a value rather than relying on the non-local control flow of exceptions.

### Exercises

1.  **Optional Chain:** Write a function `get_name(int id)` that returns `std::optional<std::string>`. Return a name for `id > 0` and `std::nullopt` otherwise.

    - _Task:_ In `main`, call the function, check if the value exists, and if so, print the string in uppercase. If not, print "User not found."
    - _Hint:_ Use `if (auto name_opt = get_name(id)) { ... }` to safely check and extract the value in one line.

2.  **Variant State Management:** Define a `Resource` variant that can hold either a `std::string` (path) or an `int` (file descriptor).

    - _Task:_ Instantiate the variant with a string, then change its value to an integer. Then, try to use `std::get<std::string>(resource)` when it holds the integer and observe the `std::bad_variant_access` exception.
    - _Hint:_ You can use `std::get_if<T>(&variant)` to attempt a safe, non-throwing check (returns `nullptr` on failure).

3.  **Variant Exhaustive Visit:** Write a single generic lambda function using **`if constexpr`** logic inside it to act as a visitor.

    - _Task:_ Use `std::visit` with this lambda on the `Resource` variant from Exercise 2. The lambda must handle the `std::string` type (print length) and the `int` type (print a status message).
    - _Hint:_ The generic lambda should take `(auto&& arg)`. Inside the body, use `if constexpr (std::is_same_v<std::decay_t<decltype(arg)>, std::string>)`.

4.  **Any Safety vs. Danger:** Create an `std::any` object, assign it a `double` value.
    - _Task:_ Safely retrieve the value using `std::any_cast<double>()`. Then, try to retrieve the value using `std::any_cast<int>()` and catch the resulting exception.
    - _Hint:_ The exception type is `std::bad_any_cast`. The compiler _cannot_ prevent the failure; it's a necessary run-time check.

---

## 17. Standard Containers and Iterators

The **Standard Template Library (STL)** in C++ provides a comprehensive suite of container classes that manage collections of objects. These containers, along with iterators and algorithms (Chapter 18), form the backbone of generic C++ programming. Unlike C\# where all collection types live in managed memory, C++ containers give you explicit control over memory layout and performance characteristics.

## 17.1 Contiguous Containers

These containers store elements in a single, block of memory, ensuring that elements are physically adjacent. This is the fastest layout for traversing, cache locality, and interoperability with raw C-style arrays.

### `std::vector` (The Workhorse)

`std::vector<T>` is the dynamic array container, and it is the single most important and frequently used container in C++. It is the C++ equivalent of C#'s `List<T>`.

| Characteristic        | Description                                                                                                                                                   | Comparison to C#                                                                             |
| :-------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------- |
| **Contiguous Memory** | Elements are guaranteed to be stored sequentially in memory.                                                                                                  | Critical for performance and passing data to C APIs.                                         |
| **Random Access**     | Accessing any element is $O(1)$ time complexity (constant time).                                                                                              | Same as array indexing (`[]`).                                                               |
| **Resizing**          | When the vector runs out of capacity, it allocates a _new, larger_ block of memory (usually double the size) and copies all old elements to the new location. | This reallocation/copy is an $O(N)$ operation, which is why pre-reserving capacity is vital. |

```cpp
#include <vector>
#include <iostream>

void demonstrate_vector() {
    std::vector<int> numbers; // Empty vector

    // Reserve capacity to avoid expensive reallocations
    numbers.reserve(100);

    for (int i = 0; i < 5; ++i) {
        numbers.push_back(i * 10); // Adds element to the end
    }

    // Access elements (O(1))
    std::cout << numbers[2] << "\n"; // Access without bounds checking
    // std::cout << numbers.at(10); // Access with bounds checking (throws std::out_of_range)

    // Contiguous guarantee:
    int* raw_ptr = numbers.data(); // Get pointer to the first element
    // raw_ptr[3] is equivalent to numbers[3]
}
```

### `std::array`

`std::array<T, N>` is a fixed-size container that wraps a raw C-style array. The size $N$ is a **non-type template parameter** and must be known at compile time.

- **Fixed Size:** Cannot be resized at runtime.
- **Stack Allocation:** The memory is typically allocated on the stack (or inside the containing object), offering zero allocation overhead.
- **Performance:** Optimal performance, often used instead of raw C-style arrays for type safety and standard library integration.

## 17.2 Sequence Containers

Sequence containers manage a sequential ordering of elements but do not necessarily store them contiguously. They are optimized for non-centralized insertions and deletions.

### `std::list` (Doubly Linked List)

`std::list<T>` is C++'s implementation of a doubly linked list (equivalent to C#'s `LinkedList<T>`).

- **Insertion/Deletion:** $O(1)$ complexity for insertion and deletion anywhere in the list, _provided you already have an iterator to the position_.
- **Random Access:** Slow $O(N)$ complexity to find an element by index because it must traverse the list.
- **Memory:** Elements are not contiguous; each node requires memory for the data plus pointers to the next and previous elements.

### `std::deque` (Double-Ended Queue)

`std::deque<T>` (pronounced "deck") is a container that supports efficient insertion and deletion at **both the beginning and the end**.

- **Memory:** Implemented as a collection of fixed-size blocks (segments), making it _mostly_ contiguous but not strictly so.
- **Performance:** Fast $O(1)$ for `push_front`, `push_back`, `pop_front`, and `pop_back`.
- **Random Access:** Supports $O(1)$ random access, making it a good alternative to `std::vector` when frequent insertions at the front are required.

## 17.3 Associative Containers (Ordered)

These containers store elements and order them according to a key (maps) or the element value itself (sets). They are typically implemented using **self-balancing binary search trees** (Red-Black Trees).

| Container                 | C# Analogue              | Key Feature                                   | Complexity (Insertion/Lookup) |
| :------------------------ | :----------------------- | :-------------------------------------------- | :---------------------------- |
| **`std::map<K, V>`**      | `SortedDictionary<K, V>` | Stores (Key, Value) pairs, sorted by **Key**. | $O(\log N)$                   |
| **`std::set<T>`**         | `SortedSet<T>`           | Stores unique elements, sorted by **Value**.  | $O(\log N)$                   |
| **`std::multimap<K, V>`** | N/A                      | Allows duplicate keys.                        | $O(\log N)$                   |
| **`std::multiset<T>`**    | N/A                      | Allows duplicate elements.                    | $O(\log N)$                   |

```cpp
#include <map>
#include <string>

void demonstrate_map() {
    std::map<std::string, int> ages;
    ages["Bob"] = 30; // Insertion/update O(log N)
    ages.insert({"Alice", 25});

    // Look up O(log N)
    if (ages.count("Bob")) {
        std::cout << "Bob's age: " << ages["Bob"] << "\n";
    }

    // Iteration is always in key-sorted order
    for (const auto& [name, age] : ages) {
        std::cout << name << " is " << age << "\n";
    }
}
```

The **ordering guarantee** is the defining characteristic of these containers.

## 17.4 Unordered Containers (Hash-Based)

These containers use **hash tables** for storage. They forgo the ordering guarantee in exchange for superior performance. They are the direct equivalents of C#'s standard dictionary and set types.

| Container                      | C# Analogue        | Key Feature                               | Complexity (Insertion/Lookup)     |
| :----------------------------- | :----------------- | :---------------------------------------- | :-------------------------------- |
| **`std::unordered_map<K, V>`** | `Dictionary<K, V>` | Stores (Key, Value) pairs, based on hash. | $O(1)$ average, $O(N)$ worst-case |
| **`std::unordered_set<T>`**    | `HashSet<T>`       | Stores unique elements, based on hash.    | $O(1)$ average, $O(N)$ worst-case |

- **Performance:** Lookup, insertion, and deletion are, on average, $O(1)$ (constant time). This makes them the container of choice when fast lookups are paramount and element order is irrelevant.
- **Requirements:** The key type `K` or element type `T` **must** provide a hash function (`std::hash<T>`) and an equality comparison operator (`operator==`).

## 17.5 Iterators: Concepts, Categories, and Range-based Operations

The STL is designed around the concept of the **iterator**, which acts as a generic pointer or handle that points to an element within a container. Iterators provide a **unified interface** for all containers, allowing algorithms to work seamlessly across vectors, lists, maps, etc. They are C++'s version of C#'s `IEnumerator<T>` but with much richer capabilities.

### Iterator Categories

Iterators are classified by the minimum set of operations they support. This classification determines which algorithms can be used with which containers.

| Category                   | Supported Operations                                             | Example Containers                                      |
| :------------------------- | :--------------------------------------------------------------- | :------------------------------------------------------ |
| **Input Iterator**         | Read once, advance only (`*it`, `it++`, `it == it2`).            | Reading from an input stream (`std::istream_iterator`). |
| **Forward Iterator**       | Input Iterator + Can read/write multiple times.                  | `std::forward_list`.                                    |
| **Bidirectional Iterator** | Forward Iterator + Can move backward (`it--`).                   | **`std::list`**, `std::set`, `std::map`.                |
| **Random Access Iterator** | Bidirectional Iterator + Pointer arithmetic (`it + n`, `it[n]`). | **`std::vector`**, `std::array`, `std::deque`.          |

The random access category is the most powerful and fastest, enabling the use of highly optimized algorithms like `std::sort`.

### The Range-Based `for` Loop

The modern C++ **range-based `for` loop** is the simplest way to iterate over any container. It internally uses the container's `begin()` and `end()` iterators.

```cpp
std::vector<int> data = {1, 2, 3, 4};

// Uses iterators internally
for (int val : data) {
    std::cout << val << " ";
}
std::cout << "\n";

// Manual iterator usage (what the range-based loop does):
for (auto it = data.begin(); it != data.end(); ++it) {
    std::cout << *it << " "; // *it dereferences the iterator to get the element
}
```

### Constant Iterators

All containers provide both mutable iterators (`begin()`, `end()`) and constant iterators (`cbegin()`, `cend()`). **Constant iterators** are essential for methods that take a `const` container reference, as they prevent modification of the container's elements.

## Key Takeaways

- **`std::vector` is the Default:** For most scenarios, use `std::vector` first due to its contiguous memory layout, cache friendliness, and $O(1)$ random access. Use `.reserve()` to mitigate reallocation overhead.
- **Ordered vs. Unordered:** Choose the appropriate map/set:
  - **`std::map`/`std::set`**: Use for guaranteed **ordering** and $O(\log N)$ complexity.
  - **`std::unordered_map`/`std::unordered_set`**: Use for maximum **speed** ($O(1)$ average) when ordering is not needed.
- **Iterators are the Core Abstraction:** Iterators provide the unified interface for traversal, enabling C++ algorithms to work across diverse data structures.
- **Iterator Categories Define Capability:** The category of a container's iterator (e.g., Random Access for `vector`, Bidirectional for `list`) dictates its performance characteristics and which algorithms it can support.
- **Use Range-Based `for`:** Prefer the modern **range-based `for` loop** for simple container traversal, as it's cleaner and safer than manual iterator management.

### Exercises

1.  **Vector Performance Trade-Off:** Create a `std::vector<int>`.

    - _Task:_ Use a loop to call `push_back(i)` 100,000 times. Print the vector's `size()` and `capacity()` at three key points: after 1 element, after 10 elements, and after the final element. Explain what `capacity()` represents and how it minimizes reallocation cost.
    - _Hint:_ The vector's capacity grows exponentially (often doubles) to avoid resizing on every single insertion.

2.  **Ordered vs. Unordered Lookup:** Create both a `std::map<int, std::string>` and a `std::unordered_map<int, std::string>`. Insert 10,000 unique elements into each.

    - _Task:_ Time the lookup process (`.find(key)`) for a key that exists and one that doesn't. Comment on the complexity difference ($O(\log N)$ vs. $O(1)$ average) and why the unordered map is generally faster.
    - _Hint:_ You can use `std::chrono::high_resolution_clock` to time the operations (Chapter 19).

3.  **List vs. Vector Insertion:** Create a `std::vector<int>` and a `std::list<int>`, both populated with 10 elements.

    - _Task:_ Measure the time taken to insert a new element at the **beginning** of both containers (`.insert(begin(), value)` for the vector, `.push_front(value)` for the list). Explain why the list is dramatically faster.
    - _Hint:_ Vector requires shifting all existing elements for a front insertion ($O(N)$), while list only needs to redirect pointers ($O(1)$).

4.  **Iterator Category Constraint:** Write a function that takes two iterators `begin` and `end` and is designed to sort the range.
    - _Task:_ Attempt to call the function with iterators from a `std::list` (Bidirectional) and then with iterators from a `std::vector` (Random Access). Explain why `std::sort` can only work efficiently, or at all, with Random Access iterators.
    - _Hint:_ Efficient sorting algorithms require the ability to jump around in memory, which is only possible with Random Access iterators.

---

## 18. The Standard Algorithms Library and Ranges (C++20)

The C++ Standard Library's greatest strength lies in its separation of concerns: **containers** (data structures, Chapter 17) and **algorithms** (logic). This separation is possible because all algorithms operate purely on **iterators**. This chapter explores the traditional algorithms library and the modern, expressive **Ranges** library introduced in C++20, which brings a LINQ-like fluency to C++.

## 18.1 The Power of `<algorithm>` and `<numeric>`

The Standard Template Library (STL) provides over 100 algorithms that replace common, error-prone manual loops. Using these standard algorithms often results in faster, more readable, and bug-free code compared to writing custom loops.

The core algorithms are found in two headers:

1.  **`<algorithm>`:** Contains most general-purpose operations like sorting, searching, transforming, and copying.
2.  **`<numeric>`:** Contains algorithms designed for numerical operations, like accumulation and inner products.

### The Iterator-Based Contract

Nearly all standard algorithms follow the same signature pattern: they take a pair of iterators defining the **range** of elements to operate on: `[begin, end)`.

```cpp
// General Algorithm Signature:
// algorithm_name(first_iterator, last_iterator, [optional_arguments...])
```

The range is half-open, meaning it includes the element pointed to by `first_iterator` but **excludes** the element pointed to by `last_iterator`.

## 18.2 Common Algorithms: Search, Sort, Transform, Accumulate

Using algorithms makes code intent clear and concise.

### Sorting and Searching (`<algorithm>`)

- **`std::sort(begin, end)`:** Sorts the elements in the range. Requires **Random Access Iterators** (e.g., `std::vector`, `std::array`). $O(N \log N)$ complexity.
- **`std::find(begin, end, value)`:** Returns an iterator to the first occurrence of `value` in the range, or the `end` iterator if not found. $O(N)$ complexity.

### Transformation and Modification (`<algorithm>`)

- **`std::transform(input_begin, input_end, output_begin, unary_op)`:** Applies a unary operation (`unary_op`) to every element in the input range and stores the result in the output range, starting at `output_begin`.
- **`std::copy(src_begin, src_end, dest_begin)`:** Copies elements from one range to another.

### Numerical Operations (`<numeric>`)

- **`std::accumulate(begin, end, initial_value, binary_op)`:** Computes the sum (or applies a custom binary operation) of all elements in the range, starting with `initial_value`.

```cpp
#include <vector>
#include <algorithm>
#include <numeric>

void demonstrate_algorithms() {
    std::vector<int> nums = {5, 2, 8, 1};

    // 1. Sort
    std::sort(nums.begin(), nums.end()); // nums is now {1, 2, 5, 8}

    // 2. Find
    auto it = std::find(nums.begin(), nums.end(), 5);
    if (it != nums.end()) {
        std::cout << "Found 5 at index: " << std::distance(nums.begin(), it) << "\n";
    }

    // 3. Accumulate (Initial value 0)
    int sum = std::accumulate(nums.begin(), nums.end(), 0);
    std::cout << "Sum: " << sum << "\n"; // Output: 16
}
```

## 18.3 Using Lambdas and Function Objects with Algorithms

Many algorithms are customizable, allowing you to pass your own logic (known as a **callable**) to define the comparison, transformation, or predicate rules. These callables can be function pointers, function objects (functors), or, most commonly in modern C++, **lambda expressions** (Chapter 14).

### Predicates and Custom Comparisons

Algorithms like `std::sort` and `std::find_if` use predicates (callables that return a `bool`).

- **`std::sort(begin, end, comparator)`:** Uses the comparator to define the ordering.
- **`std::find_if(begin, end, predicate)`:** Returns an iterator to the first element for which the predicate returns `true`.

```cpp
// 1. Custom Sort using a Lambda
std::vector<std::string> words = {"apple", "fig", "banana"};

// Sort by string length (short to long)
std::sort(words.begin(), words.end(),
    [](const std::string& a, const std::string& b) {
        return a.length() < b.length();
    });
// words is now {"fig", "apple", "banana"}

// 2. Find If using a Lambda
auto it_long = std::find_if(words.begin(), words.end(),
    [](const std::string& s) {
        return s.length() > 5; // Predicate: Is length greater than 5?
    });
// it_long points to "banana"

// 3. Transform using a Lambda
std::vector<int> output(3);
std::transform(words.begin(), words.end(), output.begin(),
    [](const std::string& s) {
        return s.length(); // Operation: Convert string to its length
    });
// output is {3, 5, 6}
```

This expressive power, enabled by lambdas, is what makes C++ data processing look similar to C\#'s LINQ.

## 18.4 Introduction to Ranges (C++20): Views and Adaptors

The Ranges library (header `<ranges>`, C++20) modernizes the STL by operating directly on the container (the **range**) instead of manual iterator pairs. This dramatically simplifies syntax and enables composability.

### From Iterators to Ranges

In the traditional STL, every operation requires specifying `c.begin(), c.end()`. Ranges allow you to pass the container `c` directly.

| Operation | Traditional STL                      | C++20 Ranges                |
| :-------- | :----------------------------------- | :-------------------------- |
| Sorting   | `std::sort(v.begin(), v.end());`     | `std::ranges::sort(v);`     |
| Finding   | `std::find(v.begin(), v.end(), 42);` | `std::ranges::find(v, 42);` |

### Views and Lazy Evaluation

The core power of Ranges comes from **Views**. A View is a lightweight, non-owning range adaptor that provides a new perspective on an existing container **without copying or modifying the underlying data**.

- **Views are Lazy:** Transformations are not executed until you actually iterate over the view (deferred execution, like C\# LINQ).
- **Views are Composable:** They can be chained together using the pipe operator (`|`).

Common views include:

- **`std::views::filter`**: Selects elements based on a predicate.
- **`std::views::transform`**: Applies a function to each element.
- **`std::views::reverse`**: Traverses the range backward.

## 18.5 The Pipelining of Algorithms

Ranges enable a functional, fluent style of programming using the pipe operator (`|`), making multi-step data processing clear and readable—a strong parallel to C#'s LINQ method chaining.

```cpp
#include <ranges>
#include <vector>
#include <iostream>

void demonstrate_ranges() {
    std::vector<int> numbers = {1, 10, 3, 20, 5, 30, 7};

    // Goal: Filter odd numbers, square them, and print the result.

    // The Range Pipeline (similar to: numbers.Where(is_odd).Select(square).ForEach(print))
    auto result_view = numbers
        | std::views::filter([](int n) {
              return n % 2 != 0; // 1, 3, 5, 7
          })
        | std::views::transform([](int n) {
              return n * n; // 1, 9, 25, 49
          });

    // The calculation only happens here (lazy evaluation)
    for (int n : result_view) {
        std::cout << n << " ";
    }
    // Output: 1 9 25 49

    // Note: The 'numbers' vector remains unchanged.
}
```

Pipelining dramatically improves code quality by separating concerns and making the flow of data transformations highly visible. When moving data between threads or when memory is limited, the lazy nature of views is a major performance and memory advantage.

## Key Takeaways

- **Algorithms = Logic + Iterators:** C++ algorithms abstract common operations over any container that provides the necessary iterator category (Chapter 17).
- **Two Core Headers:** Use `<algorithm>` for general sequence operations and `<numeric>` for mathematical reductions.
- **Custom Logic with Lambdas:** Use lambdas extensively to provide custom predicates (`std::find_if`), comparators (`std::sort`), and transformations (`std::transform`) to standard algorithms.
- **Ranges are the Modern STL (C++20):** The Ranges library simplifies algorithm syntax by operating on the container directly, replacing verbose iterator pairs.
- **Pipelining with Views:** Ranges introduce **Views**, which are lazy, composable, and non-mutating transformations. The pipe operator (`|`) allows you to chain multiple operations fluently, offering C++ a powerful, LINQ-like style.

### Exercises

1.  **Lambda Predicate with `std::count_if`:** Create a `std::vector<double>`.

    - _Task:_ Use `std::count_if` and a lambda to count how many elements in the vector are greater than $10.0$ and less than $20.0$.
    - _Hint:_ The lambda predicate should take a `double` argument and return a `bool`.

2.  **Using `std::transform`:** Create a `std::vector<int>` containing numbers from 1 to 5.

    - _Task:_ Use `std::transform` to compute the square of each element and store the result **in a new vector**.
    - _Hint:_ You need to declare the new vector first (pre-sized or empty) and use the `.begin()` iterator of the new vector as the output iterator.

3.  **Basic Range View:** Create a `std::vector<std::string>` of names.

    - _Task:_ Use `std::views::reverse` to create a view that iterates over the names backward. Print the names using a range-based `for` loop on the view.
    - _Hint:_ The expression will look like `for (const auto& name : names | std::views::reverse)`.

4.  **Range Pipelining (LINQ-style):** Use the `std::vector<int>` from Exercise 2 (1 to 5).
    - _Task:_ Create a single pipeline that first filters the numbers to keep only the **even** ones, then transforms the remaining numbers by **multiplying them by 10**, and finally prints the result using a range-based `for` loop.
    - _Hint:_ Chain `std::views::filter` and `std::views::transform` using the `|` operator. The expected output should be `20 40`.

---

## 19. Introduction to Concurrency

Concurrency in C++ involves executing multiple instruction sequences (threads) potentially at the same time. Unlike C\#, which runs in a managed environment with a sophisticated memory model, C++ concurrency operates at the OS and hardware level. This grants high performance but places a significant responsibility on the programmer to manage shared resources and prevent race conditions.

## 19.1 The C++ Memory Model and Data Races

Before synchronizing, you must understand the primary danger of multithreading in C++: the **Data Race**.

### The C++ Memory Model

The C++ Memory Model, standardized in C++11, defines the rules for how threads interact with memory. It specifies what optimization compilers and hardware can perform and what guarantees they must provide regarding memory visibility.

The key takeaway is that without explicit synchronization, the compiler is free to assume code runs sequentially within a single thread. This can lead to unexpected behavior when memory is shared.

### The Data Race: Undefined Behavior

A **Data Race** occurs when:

1.  Two or more threads access the same memory location concurrently.
2.  At least one of the accesses is a **write** operation.
3.  The threads are **not** using any explicit synchronization mechanism (like a mutex) to control access.

**Crucial Warning:** If your C++ program contains a data race, the behavior of the entire program is **Undefined Behavior (UB)**. UB means the program might crash, produce incorrect results, or appear to work fine in development but fail unpredictably in production. This is much more severe than simple race conditions in managed code.

## 19.2 The `std::thread` Basics

The `<thread>` header provides the **`std::thread`** class for managing execution threads.

### Thread Creation

A thread is created by constructing a `std::thread` object and passing the function (or any callable object like a lambda) that the thread should execute.

```cpp
#include <thread>
#include <iostream>

void worker_function(int id) {
    std::cout << "Worker thread " << id << " starting...\n";
    // Simulate work
    std::this_thread::sleep_for(std::chrono::milliseconds(100));
    std::cout << "Worker thread " << id << " finished.\n";
}

void launch_thread() {
    // Thread is created and immediately starts executing worker_function(1)
    std::thread t1(worker_function, 1);

    // ... main thread continues execution ...

    // CRITICAL: Must join or detach before t1 goes out of scope
    t1.join();
}
```

### Joining vs. Detaching (Mandatory Cleanup)

Every `std::thread` object must be explicitly dealt with before it is destroyed. Failing to do so results in a program crash (calling `std::terminate`).

1.  **`t.join()`:** Blocks the calling thread (e.g., the main thread) until thread `t` has finished execution. This is the safest and most common option.
2.  **`t.detach()`:** Separates the execution thread from the `std::thread` object. The execution thread continues to run in the background (a "daemon" thread), and the operating system handles its resources upon completion. The `std::thread` object becomes unjoinable.

## 19.3 Synchronization Primitives: `std::mutex` and Locks

The **`std::mutex`** (mutual exclusion) is the fundamental tool for synchronizing access to shared data and avoiding data races. It allows only one thread to hold the lock at any given time.

A **Critical Section** is the block of code that accesses shared resources and must be protected by a mutex.

### Basic Mutex Usage (Manual Locking)

```cpp
#include <mutex>

int shared_data = 0;
std::mutex data_mutex; // The mutex object

void unsafe_increment() {
    // No lock - DATA RACE!
    shared_data++;
}

void safe_increment() {
    data_mutex.lock(); // 1. Acquire the lock (blocks until available)

    // Critical Section
    shared_data++;

    data_mutex.unlock(); // 2. Release the lock
}
```

**Danger of Manual Locking:** If the critical section throws an exception (or has multiple exit points), the `unlock()` call might be skipped, leading to a **deadlock** where the mutex is never released. For safe, robust C++, manual locking should be avoided.

## 19.4 The `std::future` and `std::promise` for Asynchronous Results

Often, a thread is launched to perform a calculation whose result is needed later. `std::future` and `std::promise` (or the helper `std::async`) provide a type-safe mechanism to retrieve this result, similar to `Task<T>` in C#.

### `std::promise` and `std::future`

- **`std::promise<T>`:** Used by the worker thread to asynchronously set a result or an exception.
- **`std::future<T>`:** Used by the calling thread to retrieve the result set by the `promise`.

### The `std::async` Helper

**`std::async`** is the easiest way to perform a function call asynchronously and automatically manage the `promise`/`future` setup. It acts much like C#'s `Task.Run`.

```cpp
#include <future>
#include <iostream>

long long calculate_sum(int max_val) {
    long long sum = 0;
    for (int i = 1; i <= max_val; ++i) { sum += i; }
    return sum;
}

void demonstrate_async() {
    // Launch the calculation asynchronously. The return value is a future<long long>.
    std::future<long long> future_result =
        std::async(std::launch::async, calculate_sum, 10000);

    // Main thread can continue doing other work...
    std::cout << "Main thread continuing...\n";

    // Block and wait for the result (or use future_result.wait_for() for non-blocking check)
    long long result = future_result.get();

    std::cout << "Asynchronous result: " << result << "\n";
}
```

The `future.get()` call waits (blocks) until the result is available and retrieves it. It can only be called once per future.

## 19.5 Using Concurrency with RAII: `std::lock_guard` and `std::unique_lock`

To prevent deadlocks from forgotten `unlock()` calls, C++ mandates the use of **RAII (Resource Acquisition Is Initialization)** wrappers for synchronization. These wrappers acquire the lock in their constructor and guarantee its release in their destructor, even if an exception is thrown.

### `std::lock_guard` (Simple and Safe)

**`std::lock_guard<MutexType>`** is the simplest and preferred RAII wrapper for mutual exclusion. It acquires the lock immediately in its constructor and holds it for the scope of the `lock_guard` object. It cannot be unlocked early or moved.

```cpp
// Correct, RAII-safe way to use the mutex
void safe_increment_raii(std::mutex& m, int& data) {
    // Lock is acquired.
    std::lock_guard<std::mutex> lock(m);

    // Critical Section: If an exception is thrown here,
    // the 'lock' destructor is still called, releasing the mutex.
    data++;

    // Lock is automatically released when 'lock' goes out of scope.
}
```

### `std::unique_lock` (Flexible and Advanced)

**`std::unique_lock<MutexType>`** is a more flexible RAII wrapper. It is more expensive than `std::lock_guard` but supports advanced features:

1.  **Deferred Locking:** You can construct the lock object without acquiring the lock immediately (`std::defer_lock`).
2.  **Explicit Control:** You can call `lock()` and `unlock()` manually on the `unique_lock` object, but still guarantee release via the destructor.
3.  **Transferability:** `std::unique_lock` objects can be moved (e.g., passed to functions).
4.  **Condition Variables:** It is required for use with `std::condition_variable`, which handles complex waiting/notification patterns.

```cpp
void flexible_locking(std::mutex& m) {
    // Create the unique_lock object but DON'T acquire the lock yet
    std::unique_lock<std::mutex> lock(m, std::defer_lock);

    if (/* condition to lock is met */) {
        lock.lock(); // Acquire lock only if needed
        // ... critical section ...
    }
    // Lock is still guaranteed to be released if acquired.
}
```

## Key Takeaways

- **Data Race = UB:** The primary rule of C++ concurrency is to **NEVER** allow a data race. Any simultaneous read/write to shared, unsynchronized memory leads to **Undefined Behavior**.
- **Thread Cleanup is Mandatory:** Every `std::thread` object must be explicitly handled with either **`.join()`** (wait for completion) or **`.detach()`** (run independently) before it is destroyed.
- **RAII Locks are Non-Negotiable:** Avoid manual `mutex::lock()` and `mutex::unlock()`. Use **`std::lock_guard`** for simple scope-based critical sections and **`std::unique_lock`** when advanced features like deferred locking or condition variables are needed.
- **`std::async` for Results:** Use **`std::async`** (which returns a **`std::future`**) as the high-level way to launch asynchronous operations and safely retrieve the result non-blockingly, similar to C#'s `Task.Run`.

### Exercises

1.  **Thread Join vs. Detach:** Write a short program that launches two threads: `t1` and `t2`. Thread `t1` should print a message every 100ms for 5 iterations. Thread `t2` should do the same.

    - _Task:_ Set `t1.join()` and `t2.detach()`. Observe the output. Explain the lifetime difference and why detaching `t2` means the main program might exit before `t2` finishes its last message.
    - _Hint:_ The program will wait for `t1` but not `t2`.

2.  **Data Race Demonstration:** Create a simple integer `counter` initialized to 0. Create two threads, where each thread increments the counter $10,000$ times without using any synchronization.

    - _Task:_ Run the program and print the final value of the counter. Explain why the result is almost certainly not $20,000$ and how this demonstrates a data race.
    - _Hint:_ The increment operation (`counter++`) is not atomic; it involves read, modify, and write steps which can interleave.

3.  **RAII Mutex Fix:** Take the data race code from Exercise 2.

    - _Task:_ Introduce a `std::mutex` and use a **`std::lock_guard`** inside the increment function to protect the counter access. Verify that the final result is exactly $20,000$.
    - _Hint:_ The `std::lock_guard` object must be constructed inside the function that accesses the shared data.

4.  **Asynchronous Summation:** Write a function `sum_range(int start, int end)` that calculates the sum of a number range.
    - _Task:_ Use two separate calls to **`std::async`** to calculate the sum of the range 1 to 50,000 and the range 50,001 to 100,000 concurrently. Then, use `.get()` on both futures and add the results to find the total sum.
    - _Hint:_ The total sum should be $5,000,050,000$. The call to `std::async` should use `std::launch::async` to ensure true parallel execution.

---

## Where to go Next

- **[Part I:](./part1.md): The C++ Ecosystem and Foundation:** This section establishes the philosophical and technical underpinnings of C++, focusing on compilation, linking, and the modern modularization system.
- **[Part II](./part2.md): Core Constructs, Classes, and Basic I/O:** Here, we cover the essential C++ syntax, focusing on differences in data types, scoping, **`const` correctness**, and the function of **lvalue references**.
- **[Part III](./part3.md): The C++ Memory Model and Resource Management:** The most critical section, which deeply explores raw pointers, value categories, **move semantics**, and the indispensable role of **smart pointers** and the \*\*RAII\*\* idiom.
- **[Part IV](./part4.md): Classical OOP, Safety, and Type Manipulation:** This part addresses familiar object-oriented concepts like **inheritance** and **polymorphism**, emphasizing C++'s rules for \*\*exception safety\*\* and type-safe casting.
- **[Part V](./part5.md): Genericity, Modern Idioms, and The Standard Library:** Finally, we explore the advanced capabilities of **templates**, **C++20 Concepts**, **lambda expressions**, and the power of the **Standard Library containers** and \*\*Ranges\*\* for highly generic and expressive code.
- **[Appendix](./appendix.md):** Supplementary materials including coding style guidelines, compiler flags, and further reading.
