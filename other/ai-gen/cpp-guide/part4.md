---
layout: default
title: Programming in C++ Part IV | Jakub Smolik
---

[..](./index.md)

[View as PDF](./part4.pdf)

# Part IV: Classical OOP, Safety, and Type Manipulation

This part delves into advanced object-oriented programming concepts and type safety in C++. Chapters 10 through 13 guide you through robust error handling with exceptions, the nuances of inheritance and polymorphism, and the mechanisms for safe and explicit type conversions. You'll learn how C++ enforces exception safety, how to design class hierarchies with virtual functions and destructors, and how to use casting operators and run-time type information responsibly. By mastering these topics, you'll be equipped to write safer, more maintainable, and idiomatic C++ code that leverages the language's powerful type system.

## Table of Contents

#### [10. Error Handling and Exceptions](#10-error-handling-and-exceptions-1)

- 10.1 Throwing and Catching Exceptions
- 10.2 Creating Custom Exception Classes
- 10.3 **Exception Safety Guarantees** (Strong, Basic, Nothrow)
- 10.4 The Role of `noexcept` and `noexcept(foo)`

#### [11. Inheritance and Polymorphism](#11-inheritance-and-polymorphism-1)

- 11.1 **Public, Protected, and Private Inheritance**
- 11.2 **Virtual Methods** and Dynamic Dispatch
- 11.3 Abstract Base Classes and Pure Virtual Functions (Interfaces)
- 11.4 The `override` and `final` Specifiers
- 11.5 **Virtual Destructors** and Deleting Polymorphic Objects
- 11.6 **Virtual Inheritance** (The Diamond Problem Solution)

#### [12. Type Conversions and Explicit Constructors](#12-type-conversions-and-explicit-constructors-1)

- 12.1 Implicit Type Conversions (Promotion and Conversion)
- 12.2 User-Defined Conversion Operators
- 12.3 Preventing Conversions with the **`explicit`** Keyword
- 12.4 Uniform Initialization and `std::initializer_list`

#### [13. Casting Operators and RTTI](#14)

- 13.1 C-Style Casts: Why They Are Dangerous
- 13.2 **`static_cast`**: Compile-Time Conversions
- 13.3 **`dynamic_cast`**: Run-Time Polymorphic Checking
- 13.4 **`const_cast`** and **`reinterpret_cast`**: High-Risk Operations
- 13.5 Run-Time Type Information (RTTI) and `typeid`

---

## 10. Error Handling and Exceptions

Like most modern languages, C++ uses exceptions as the primary mechanism for signaling and handling errors that occur outside the regular flow of control. While the mechanics of **`try`/`catch`/`throw`** will be familiar to you, C++ exception handling is unique due to its integration with the **RAII principle** (Chapter 9) and the resulting focus on **Exception Safety Guarantees**.

## 10.1 Throwing and Catching Exceptions

The fundamental syntax for throwing and catching exceptions is highly similar to C\#.

### Throwing

You can throw any expression, but it's best practice to throw an object derived from `std::exception`.

```cpp
#include <stdexcept>
#include <string>

void validate_input(int value) {
    if (value < 0) {
        // Throw an object derived from std::runtime_error
        throw std::runtime_error("Input value cannot be negative.");
    }
}
```

### Catching

You use `try` and `catch` blocks. The crucial best practice in C++ is to catch exceptions **by `const` reference**.

```cpp
#include <iostream>

void process_data(int val) {
    try {
        validate_input(val);
        // ... rest of logic ...
    }
    // Catching by const reference avoids slicing and unnecessary copying.
    catch (const std::runtime_error& e) {
        std::cerr << "Runtime Error: " << e.what() << "\n";
        // Optionally re-throw the original exception
        // throw;
    }
    // Catching all others (ellipses) should be avoided where possible
    catch (...) {
        std::cerr << "An unknown exception occurred.\n";
    }
}
```

**Catching by `const std::exception&`** is vital because catching by value can lead to **slicing** (where the derived exception object is truncated to its base class type) and unnecessary object copying.

## 10.2 Creating Custom Exception Classes

For application-specific error conditions, you should create custom exception classes that derive from a standard base class.

- **`std::logic_error`:** Used for errors that should have been preventable by the programmer (e.g., passing a null pointer, bounds check failures).
- **`std::runtime_error`:** Used for errors that are generally unavoidable and occur during program execution (e.g., file not found, network connection lost).

The standard exception classes provide a `virtual const char* what() const noexcept` method that returns a descriptive string.

```cpp
#include <stdexcept>
#include <string>

// Custom exception for a specific business logic failure
class AccountNotFoundException : public std::runtime_error {
public:
    // Pass the message to the base class constructor
    AccountNotFoundException(int account_id)
        : std::runtime_error("Account ID " + std::to_string(account_id) + " not found.") {}

    // Optionally override the what() method if more complex behavior is needed
    // virtual const char* what() const noexcept override { ... }
};
```

## 10.3 Exception Safety Guarantees

In C++, when an exception is thrown, the compiler performs **stack unwinding**: it walks up the call stack, invoking the **destructor** of every object with **Automatic Storage Duration** (Chapter 3.3) until a matching `catch` block is found. This is the **RAII mechanism** at work, ensuring that resources are released.

Since destructors are guaranteed to be called, C++ code is categorized by how well it maintains the system's state during this process. This leads to three levels of **Exception Safety Guarantees**:

### 1. The No-throw Guarantee (The Strongest)

The function is guaranteed not to throw an exception under any circumstance.

- **Mechanism:** Achieved by functions that only use primitive types, or by functions explicitly marked `noexcept` (Section 10.4).
- **Ideal Use:** Destructors, swap functions, and move operations (Chapter 8).

### 2. The Strong Guarantee (The Rollback)

If the function throws an exception, the program state remains exactly as it was before the function was called. **The operation either fully succeeds or fully fails with no side effects.**

- **Mechanism:** Often implemented using the **copy-and-swap idiom** (creating a copy of the state, modifying the copy, and swapping the original with the copy only if the entire operation succeeds).
- **Desirable Outcome:** Prevents corruption; difficult to achieve in practice.

### 3. The Basic Guarantee (The Minimum)

If the function throws an exception, no resources are leaked (thanks to RAII), and the program remains in a valid, usable state, but the exact data/values may be unpredictable.

- **Mechanism:** Achieved by making sure every resource is wrapped in an RAII object (like `std::unique_ptr` or `std::vector`).
- **Minimum Requirement:** All C++ code should achieve at least the Basic Guarantee.

**Takeaway:** The purpose of RAII (Smart Pointers, Containers) is to automatically deliver the **Basic Guarantee**. If an exception occurs, the destructors are called, and heap memory is safely cleaned up, preventing memory leaks.

## 10.4 The Role of `noexcept` and `noexcept(foo)`

The keyword **`noexcept`** is an **exception specification** that plays two key roles: providing clarity and enabling optimization.

### `noexcept` as a Promise

When a function is marked `noexcept`, you are making a **promise to the compiler** that the function will not emit an exception.

```cpp
// This function promises not to throw
void do_safe_operation() noexcept {
    // ...
}
```

### The Optimization: Avoiding Stack Unwinding Code

If the compiler knows a function will not throw, it can omit the expensive code required to unwind the stack (the logic to find and call all destructors) upon exception. This is a significant performance gain, especially for functions called frequently, like standard library functions.

**Critical Consequence:** If a function promised `noexcept` throws anyway, the C++ runtime does not attempt to unwind the stack. It immediately calls **`std::terminate()`**, which typically stops the program immediately. This is safer than continuing with a corrupted state, but it is a program crash.

### `noexcept` and Move Semantics

The `noexcept` guarantee is essential for the performance of standard library containers (like `std::vector`).

When a `std::vector` grows, it must move its elements to a new, larger block of memory. If the element's move constructor is marked `noexcept`, the vector knows it can safely move the elements without worrying about an exception corrupting the vector's state. If the move constructor is _not_ marked `noexcept`, the vector defaults to the slower **copy** operation to maintain the Strong Guarantee.

**Best Practice:** All **move constructors** and **move assignment operators** (Chapter 8) should be marked **`noexcept`** unless they truly must throw an exception.

### Conditional `noexcept(expr)`

The **`noexcept(expr)`** form is used to conditionally specify no-throw based on a compile-time boolean expression:

```cpp
template <typename T>
void swap_values(T& a, T& b) noexcept(std::is_nothrow_swappable<T>::value) {
    // std::is_nothrow_swappable is true if T's swap function is noexcept.
    // The compiler can now choose the efficient path based on T.
}
```

This allows generic code to be exception-safe without sacrificing performance.

## Key Takeaways

- **Catch by `const` Reference:** Always catch exceptions by **`const T&`** (usually `const std::exception&`) to avoid object slicing and unnecessary copying.
- **RAII Delivers Basic Safety:** The RAII principle (automatic destructor calls upon stack unwinding) ensures the **Basic Guarantee** by preventing resource leaks.
- **Strong Guarantee is the Goal:** The **Strong Guarantee** (transactional rollback) is achieved by design patterns like copy-and-swap, ensuring state is consistent even after a failure.
- **`noexcept` is a Promise and an Optimization:** Mark a function **`noexcept`** if it won't throw. This allows the compiler to optimize by omitting stack unwinding code.
- **Breaking `noexcept` Crashes:** If a `noexcept` function throws, the program immediately terminates via `std::terminate()`, avoiding expensive and potentially unsafe unwinding. **Always make move operations `noexcept`.**

### Exercises

1.  **Catching by Value vs. Reference:** Write a custom exception class `SpecificError` that inherits from `std::runtime_error`. In your `main` function, throw an instance of `SpecificError`.

    - _Task:_ Catch the exception first by value (`catch (std::exception e)`), then by reference (`catch (const std::exception& e)`). Observe that the first version only sees the `std::exception` part of the object (slicing), while the second sees the full `SpecificError` type.
    - _Hint:_ Use `e.what()` to show the different behaviors.

2.  **RAII and Exception Safety:** Create a class `Lock` that prints "Acquired" in its constructor and "Released" in its destructor. In a `try` block, create an instance of `Lock`, and then immediately `throw 0;`.

    - _Task:_ Observe that the "Released" message prints before the `catch` block executes. Explain how this demonstrates RAII providing the Basic Exception Safety Guarantee.
    - _Hint:_ The lock object's destructor is called during stack unwinding before control transfers to the `catch` handler, preventing a resource leak.

3.  **The Effect of `noexcept`:** Write two identical functions: `void fast_op() noexcept` and `void slow_op()`. In the body of each, `throw std::runtime_error("oops");`.

    - _Task:_ Wrap both function calls in separate `try/catch` blocks. Explain what happens when you run the program (one function should lead to `std::terminate`).
    - _Hint:_ The `noexcept` function breaks its promise, resulting in an immediate call to `std::terminate`, skipping the local `catch` block.

4.  **Implementing the Strong Guarantee (Conceptual):** Explain how you would modify a class `Vector` (which holds a raw array) to achieve the **Strong Guarantee** for its `resize()` method, which might throw if the new memory allocation fails.
    - _Task:_ Describe the **copy-and-swap** approach that must be used.
    - _Hint:_ The allocation and copy operation must happen on a _temporary_ internal buffer. Only if the whole process succeeds is the temporary buffer swapped into the main object's state.

---

## 11. Inheritance and Polymorphism

Inheritance and polymorphism are the cornerstones of Object-Oriented Programming (OOP) in C++, just as they are in C\#. However, C++ requires more explicit keywords to enable run-time polymorphism and offers different, more powerful, yet more complex mechanisms for controlling inheritance and memory safety.

## 11.1 Public, Protected, and Private Inheritance

C++ allows a derived class to inherit from a base class using three distinct **access modes**, specified before the base class name. This mode affects the **maximum access level** of the base class members _within the derived class_.

| Mode of Inheritance | Meaning                                                 | Effect on Base Members in Derived Class                                                                              | Typical Use                                         |
| :------------------ | :------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------- |
| **`public`**        | **Is-A** relationship (Standard OOP)                    | Preserves original access level (`public` stays `public`, `protected` stays `protected`).                            | Standard class extension.                           |
| **`protected`**     | Base `public` members become `protected`.               | `protected` members stay `protected`. `private` are still inaccessible.                                              | Restricting public access to the inheritance chain. |
| **`private`**       | Base `public` and `protected` members become `private`. | Used for **Implementation Detail** (Is-Implemented-In-Terms-Of). Users of the derived class cannot see base members. | Rarely used; prefer composition.                    |

**Example of Public Inheritance (Standard)**

```cpp
class Base {
public:
    int public_val = 1;
protected:
    int protected_val = 2;
private:
    int private_val = 3;
};

// Derived class inherits publicly: public_val is still accessible publicly via Derived objects
class Derived : public Base {
public:
    void access_members() {
        std::cout << public_val;    // OK (public)
        std::cout << protected_val; // OK (protected)
        // std::cout << private_val; // ERROR: private member inaccessible
    }
};
```

**Best Practice:** Always use **`public`** inheritance unless you have a strong, specific reason to hide the base class's interface (e.g., implementing the Adapter pattern via private inheritance).

## 11.2 Virtual Methods and Dynamic Dispatch

In C++, methods are **non-virtual by default**. To enable **run-time polymorphism** (the ability to call the correct derived method through a base-class pointer or reference), you must use the **`virtual`** keyword on the base class function.

### The Mechanism: Dynamic Dispatch

When a function is marked `virtual`, the compiler generates a hidden table for that class called the **vtable** (virtual table). This table holds pointers to the correct function implementations.

When a virtual function is called through a base-class pointer (`Base* p = new Derived();`), the C++ runtime performs **dynamic dispatch**: it looks up the function pointer in the object's vtable to determine which derived version to execute.

```cpp
class Animal {
public:
    // This enables run-time polymorphism
    virtual void make_sound() const {
        std::cout << "Animal generic sound\n";
    }
    // Non-virtual methods are statically dispatched
    void eat() const {
        std::cout << "Animal is eating\n";
    }
};

class Dog : public Animal {
public:
    // Automatically virtual if base is virtual (but use 'override'!)
    void make_sound() const override {
        std::cout << "Woof!\n";
    }
};

void test_polymorphism() {
    Animal* a = new Dog(); // Base pointer to Derived object

    a->make_sound(); // Output: Woof! (Dynamic Dispatch)
    a->eat();        // Output: Animal is eating (Static Dispatch on Base::eat)

    delete a;
}
```

If `make_sound()` were not virtual, `a->make_sound()` would incorrectly output "Animal generic sound" because the call would be resolved statically based on the pointer type (`Animal*`).

## 11.3 Abstract Base Classes and Pure Virtual Functions (Interfaces)

C++ uses **Pure Virtual Functions** to create **Abstract Base Classes (ABCs)**, serving a role similar to C\#'s `abstract` classes and `interface`s.

A pure virtual function is declared by initializing it to zero in the class declaration:

```cpp
// Pure Virtual Function
virtual ReturnType function_name(Args) = 0;
```

### Abstract Base Class (ABC)

A class containing at least one pure virtual function is considered an **Abstract Base Class**.

- **You cannot create instances of an ABC.** They can only be used as base classes for inheritance.
- Any derived class must provide an implementation for _all_ inherited pure virtual functions or it, too, will be an ABC.

```cpp
class ILogger { // Conventionally prefixed with 'I' for interface
public:
    // Pure virtual function: forces derived classes to implement logging.
    virtual void log(const std::string& message) = 0;

    // ABCs often have a virtual destructor (see 11.5)
    virtual ~ILogger() = default;
};

class ConsoleLogger : public ILogger {
public:
    // Must implement log() to be concrete
    void log(const std::string& message) override {
        std::cout << "[LOG] " << message << "\n";
    }
};
```

## 11.4 The `override` and `final` Specifiers

Modern C++ (C++11 and later) introduced specifiers to improve the safety and control of inheritance hierarchies.

### `override` (Safety)

The `override` specifier is placed after the parameter list of a derived class function. It tells the compiler: "**This function must override a virtual function in the base class.**"

- **Benefit:** Prevents subtle bugs caused by typos in the function name or parameter list, which would otherwise result in an overloaded (new) function instead of an overriding one.
- **Best Practice:** **Always** use `override` when attempting to override a base virtual function.

```cpp
class Child : public Parent {
public:
    // Correct and safe: compiler checks Base for matching virtual function
    void some_method() override { /* ... */ }

    // If Parent::some_method had a different signature, this would be a compile-time error.
    // Without 'override', it would silently create a new, non-virtual function.
};
```

### `final` (Restriction)

The `final` specifier prevents a class from being inherited further or a virtual function from being overridden further.

```cpp
// Prevents any class from inheriting from FinalClass
class FinalClass final { /* ... */ };

class BaseController {
public:
    // Prevents derived classes from overriding this specific method
    virtual void initialize() final { /* ... */ }
};
```

This is the equivalent of C\#'s `sealed` keyword.

## 11.5 Virtual Destructors and Deleting Polymorphic Objects

This is one of the most important rules for memory safety in C++ polymorphism:

> **If a class has any virtual functions, it MUST have a virtual destructor.**

### The Danger of Non-Virtual Destruction

If you delete a derived class object through a base-class pointer or reference, and the base class destructor is **non-virtual**, the C++ runtime uses **static dispatch**. It only calls the destructor for the **base class** part of the object.

```cpp
class ResourceUser {
public:
    ~ResourceUser() {
        std::cout << "Base Destructor called\n";
    }
};

class HeavyResourceUser : public ResourceUser {
private:
    int* large_buffer = new int[100]; // Resource acquired
public:
    // PROBLEM: Base class destructor is NOT virtual
    ~HeavyResourceUser() {
        std::cout << "Derived Destructor called\n";
        delete[] large_buffer;
    }
};

void unsafe_deletion() {
    ResourceUser* p = new HeavyResourceUser();
    delete p; // Calls ONLY the Base Destructor, leaking the large_buffer! (Undefined Behavior)
}
```

### The Solution: Virtual Destructor

By making the base class destructor **`virtual`**, the `delete` operator uses **dynamic dispatch** to correctly call the most derived destructor first, followed by all base destructors.

```cpp
class ResourceUser {
public:
    // Solution: virtual destructor guarantees correct destruction
    virtual ~ResourceUser() {
        std::cout << "Base Destructor called\n";
    }
};
// ... other code remains the same ...

void safe_deletion() {
    ResourceUser* p = new HeavyResourceUser();
    delete p; // Now correctly calls Derived, then Base Destructor.
}
```

**Best Practice:** If you have a class intended to be a base class, or if it has _any_ virtual functions, make its destructor **`virtual`**.

## 11.6 Virtual Inheritance (The Diamond Problem Solution)

C++ supports **Multiple Inheritance** (inheriting from more than one base class), which introduces the **Diamond Problem**.

The Diamond Problem occurs when:

1.  Class `B` and class `C` both inherit from a common base `A`.
2.  Class `D` inherits from both `B` and `C`.

The resulting object `D` will contain **two separate sub-objects** of class `A` (one inherited via `B` and one via `C`), leading to ambiguity when accessing `A`'s members.

### The Solution: Virtual Inheritance

To ensure the common base class `A` is represented only **once** in the final derived class `D`, the intermediate classes (`B` and `C`) must inherit using the **`virtual`** keyword:

```cpp
class A { /* ... */ };

// B virtually inherits A
class B : virtual public A { /* ... */ };

// C virtually inherits A
class C : virtual public A { /* ... */ };

// D inherits both B and C, but A is only included once.
class D : public B, public C { /* ... */ };
```

By using `virtual public A`, you instruct the compiler to arrange the memory layout of `D` such that the `A` sub-object is shared and exists only once. This is a complex topic usually reserved for highly specialized designs.

## Key Takeaways

- **Inheritance Modes:** **`public`** inheritance is the standard "is-a" relationship. `protected` and `private` restrict the access of base members within the derived class.
- **Explicit Polymorphism:** C++ requires the **`virtual`** keyword on the base class method to enable run-time polymorphism (**dynamic dispatch** via the vtable).
- **Abstract Classes:** Declare a function as **pure virtual** (`= 0`) to make a class an **Abstract Base Class**, which cannot be instantiated and enforces implementation in derived classes.
- **Safety Specifiers:** Use **`override`** to ensure you are correctly overriding a base method, and **`final`** to prevent further overriding or inheritance.
- **Virtual Destructors are Mandatory:** If a base class has any virtual functions, it **MUST** have a **virtual destructor** to guarantee the correct deletion of derived objects via base pointers and prevent memory leaks.
- **Virtual Inheritance:** Use **`virtual`** inheritance to solve the **Diamond Problem** in multiple inheritance by ensuring the common base class sub-object is included only once.

### Exercises

1.  **Polymorphic Failure:** Create a base class `Shape` with a non-virtual `draw()` method. Create a derived class `Circle` that overrides `draw()`.

    - _Task:_ Call `draw()` using a `Shape*` pointer pointing to a `Circle` object. Observe that the `Shape::draw()` method is called. Explain why this demonstrates static dispatch.
    - _Hint:_ Add the `virtual` keyword to `Shape::draw()` and observe the correct behavior.

2.  **The `override` Safety Net:** Take the code from Exercise 1 and make `Shape::draw()` virtual. Now, in `Circle`, intentionally misspell the method name to `Draw()` (capital D).

    - _Task:_ Add the `override` specifier to `Circle::Draw()`. Observe the **compile-time error**. Remove `override` and observe the compiler silently creates a new, non-virtual method. Explain why `override` saved you from a difficult-to-find run-time bug.
    - _Hint:_ Without `override`, the compiler thinks you're just defining a new function for `Circle`.

3.  **Virtual Destructor Leaks:** Write the unsafe deletion example from Section 11.5 (using `ResourceUser` and `HeavyResourceUser` without a virtual destructor).

    - _Task:_ Run the code and observe the output (only the base destructor called). Explain where the memory leak occurs and how adding `virtual` to `~ResourceUser()` fixes the leak.
    - _Hint:_ The derived class's destructor, which holds the `delete[] large_buffer` logic, is skipped.

4.  **Implementing an Interface (ABC):** Define an abstract base class `IResizable` with a pure virtual function `resize(int width, int height)`.
    - _Task:_ Create a concrete class `Window` that publicly inherits from `IResizable` and implements the `resize` method using the `override` keyword. Show that you cannot instantiate `IResizable`.
    - _Hint:_ An Abstract Base Class cannot be initialized; it must be derived from.

---

## 12. Type Conversions and Explicit Constructors

Type conversion is the process of changing a value from one type to another. In C#, you primarily deal with explicit casts for user-defined types. In C++, however, the compiler is aggressive about performing **implicit conversions**—a feature that is both highly convenient and a significant source of subtle bugs. Modern C++ dictates strict control over these automatic conversions using the **`explicit`** keyword.

## 12.1 Implicit Type Conversions (Promotion and Conversion)

C++ performs implicit conversions in many contexts: function calls, assignments, and initializations. These conversions fall into two main categories:

### 1. Standard Conversions (Built-in Types)

These are compiler-defined rules for primitive types:

- **Promotion:** Converting a smaller type to a larger type (e.g., `int` to `double`). This is usually safe and non-lossy.
- **Conversion:** Converting types where data loss may occur (e.g., `double` to `int` or signed to unsigned).

```cpp
double d = 10;      // Implicit promotion: 10 (int) -> 10.0 (double)
int i = 5.7;        // Implicit conversion: 5.7 (double) -> 5 (int) - Data loss!
```

### 2. User-Defined Conversions (Classes)

The compiler can use your class's constructors and operators to create a conversion chain. Two mechanisms enable implicit conversion for user-defined types:

- **Single-Argument Constructors:** A constructor that can be called with a single argument of another type implicitly tells the compiler, "I know how to turn a $\text{T}$ into a $\text{MyClass}$."
- **Conversion Operators:** A special member function that tells the compiler, "I know how to turn a $\text{MyClass}$ into a $\text{T}$."

## 12.2 User-Defined Conversion Operators

A **conversion operator** allows an instance of your class to be implicitly converted to another type (like an `int`, `bool`, or another class). The syntax is unique: it has no return type and the name is the target type preceded by the `operator` keyword.

```cpp
class Fraction {
private:
    int numerator_ = 0;
    int denominator_ = 1;

public:
    Fraction(int num, int den) : numerator_(num), denominator_(den) {}

    // User-defined implicit conversion operator: Fraction -> double
    operator double() const {
        // This allows any Fraction object to be used where a double is expected.
        return static_cast<double>(numerator_) / denominator_;
    }
};

void print_value(double val) {
    std::cout << "Value: " << val << "\n";
}

int main() {
    Fraction f{3, 4};

    // Implicit conversion is used here: f (Fraction) -> 0.75 (double)
    print_value(f); // Output: Value: 0.75

    return 0;
}
```

While concise, this automatic, silent conversion can lead to unexpected behavior when complex logic is involved, as the compiler may choose a conversion path you did not intend.

## 12.3 Preventing Conversions with the **`explicit`** Keyword

The greatest source of ambiguity and bugs in C++ is often an unwanted **implicit conversion**. The solution is the **`explicit`** keyword.

The general rule in Modern C++ is:

> **Always mark single-argument constructors and conversion operators as `explicit` unless you have a strong, specific reason for an implicit conversion.**

### 1. `explicit` Constructors

When applied to a constructor, `explicit` prevents it from being used for implicit conversions. This forces the programmer to use direct initialization or an explicit cast.

```cpp
class Id {
public:
    // This constructor IS a conversion from int to Id.
    explicit Id(int id) { /* ... */ }
};

void process_id(const Id& user_id) { /* ... */ }

int main() {
    // 1. Direct Initialization (OK with or without explicit)
    Id a{100};

    // 2. Implicit Conversion (ERROR because constructor is explicit)
    // Id b = 200;

    // 3. Implicit conversion in function call (ERROR)
    // process_id(300);

    // 4. Explicit cast required (OK)
    process_id(static_cast<Id>(300));

    return 0;
}
```

By making the constructor `explicit`, you ensure the `int` is only converted to `Id` when the programmer consciously decides to do so.

### 2. `explicit` Conversion Operators

When applied to a conversion operator, `explicit` prevents the conversion from happening implicitly (unless it is a conversion to `bool`, which is a common exception).

```cpp
class Fraction {
public:
    // ... constructor ...

    // Conversion is now EXPLICIT.
    explicit operator double() const {
        // ...
    }
};

void print_value(double val) { /* ... */ }

int main() {
    Fraction f{1, 2};

    // print_value(f); // ERROR: Cannot convert Fraction to double implicitly.

    // Explicit cast is now required
    print_value(static_cast<double>(f)); // OK

    return 0;
}
```

## 12.4 Uniform Initialization and `std::initializer_list`

C++ introduced a single, consistent syntax for initialization: **uniform initialization**, which uses braces (`{}`).

```cpp
int x {5}; // Initializes x to 5
std::vector<int> v {1, 2, 3}; // Initializes vector with elements 1, 2, 3
```

### Initialization Rules

The `explicit` keyword and uniform initialization interact predictably:

1.  **Uniform Initialization is Safer:** The brace syntax (`{...}`) does **not** allow the compiler to use an `explicit` constructor for implicit conversions, whereas parenthesis initialization (`(...)`) does in some cases. This makes brace initialization inherently safer.

    ```cpp
    class T { explicit T(int) {} };
    T t1 {5};  // OK: Direct initialization
    // T t2 = {5}; // ERROR: Copy initialization not allowed because T(int) is explicit
    ```

2.  **`std::initializer_list` Precedence:** The compiler always prefers a constructor that takes a `std::initializer_list<T>` over a regular constructor when using the brace syntax.

`std::initializer_list<T>` is a temporary, lightweight object that acts as a view into a list of elements. It is the core mechanism used by all standard containers to initialize collections.

```cpp
#include <initializer_list>
#include <iostream>

class VectorWrapper {
public:
    // This constructor takes precedence over all others when using {}
    VectorWrapper(std::initializer_list<int> list) {
        std::cout << "Using initializer_list constructor for " << list.size() << " elements.\n";
    }

    // Regular single-argument constructor
    VectorWrapper(int size) {
        std::cout << "Using regular int constructor for size " << size << ".\n";
    }
};

int main() {
    VectorWrapper w1{10};      // Output: Using initializer_list constructor (list size 1)
    VectorWrapper w2(10);      // Output: Using regular int constructor (parenthesis call)
    VectorWrapper w3{10, 20};  // Output: Using initializer_list constructor (list size 2)

    return 0;
}
```

If you intend a single-argument constructor to be called when using braces (like `w2` above), you must ensure your class does not define an `std::initializer_list` constructor that could match.

## Key Takeaways

- **Implicit Conversions are Aggressive:** C++ will use single-argument constructors and conversion operators implicitly unless told otherwise, often leading to surprising behavior.
- **Conversion Operators:** Use `operator TargetType()` to define rules for automatically converting your class into another type.
- **The `explicit` Rule:** **Always** mark single-argument constructors and user-defined conversion operators as **`explicit`** to prevent unintended implicit conversions. This is a core tenet of modern C++ safety.
- **Uniform Initialization:** Use brace initialization (`{}`) as the standard, safer way to initialize objects, as it restricts implicit conversions more aggressively than parenthesis initialization (`()`).
- **`initializer_list` Precedence:** The compiler will always prefer a constructor taking a `std::initializer_list` over any other constructor when braces are used.

### Exercises

1.  **Implicit Conversion Failure:** Create a class `UserID` with a non-`explicit` constructor `UserID(int id)`. Create a function `void check_id(const UserID& id)` that takes a `UserID` by `const` reference.

    - _Task:_ Call `check_id(42);`. Explain how the compiler automatically converted the integer into a `UserID` object, which is dangerous. Now, add `explicit` to the constructor and observe the compile-time error.
    - _Hint:_ The constructor becomes a hidden conversion path.

2.  **Explicit Conversion Operator:** Create a class `Temperature` that stores the temperature in Celsius. Add an `explicit operator double()` that converts the temperature to Fahrenheit.

    - _Task:_ Try to assign a `Temperature` object to a `double` variable without a cast. Then, use `static_cast<double>(temp)` to perform the conversion. Explain why the `explicit` keyword is necessary here.
    - _Hint:_ The conversion should only happen when the programmer consciously requests it.

3.  **Ambiguity with `initializer_list`:** Create a simple class `Wrapper` with two constructors: `Wrapper(int value)` and `Wrapper(std::initializer_list<int> list)`.

    - _Task:_ Initialize an object using `Wrapper w{5};`. Which constructor is called? Change the first constructor to `explicit` and try again.
    - _Hint:_ The `std::initializer_list` constructor takes precedence for brace initialization, even if the list size is one.

4.  **Uniform Initialization Safety:** Define a class `Point` with two integer members. Define a constructor `Point(int x, int y)`.
    - _Task:_ Initialize a point using parenthesis: `Point p1(4.5, 5.5);`. Then initialize another using braces: `Point p2{4.5, 5.5};`. Explain why the first compiles (allowing implicit conversion/truncation of the doubles to int), but the second may fail (because brace initialization disallows narrowing conversions).
    - _Hint:_ Brace initialization provides stronger type checking and prevents narrowing conversions by default.

---

## 13. Casting Operators and RTTI

While **implicit conversions** (Chapter 12) are generally discouraged, **explicit conversions** (or casting) are sometimes necessary to safely convert between types or navigate complex inheritance hierarchies. C++ provides four specialized casting operators that clearly define the programmer's intent, replacing the ambiguous and dangerous C-style cast.

## 13.1 C-Style Casts: Why They Are Dangerous

A C-style cast uses parenthesis syntax: `(TargetType)expression`.

```cpp
double value = 10.5;
int i = (int)value; // C-style cast
```

The danger of the C-style cast is its **lack of specificity**. It instructs the compiler to perform the necessary cast, which could be any of the specialized C++ casts (`static_cast`, `const_cast`, or `reinterpret_cast`), often trying the most aggressive cast until one succeeds.

| C-Style Cast Potential                | Specialized Cast Equivalent | Intent                                            | Risk Level |
| :------------------------------------ | :-------------------------- | :------------------------------------------------ | :--------- |
| Simple conversion (`double` to `int`) | `static_cast`               | Safe, verifiable.                                 | Low        |
| Removing `const`                      | `const_cast`                | High-risk, violates object safety.                | High       |
| Raw memory reinterpretation           | `reinterpret_cast`          | Extremely high-risk, low-level bits manipulation. | Maximum    |

Because the compiler hides the true nature of the cast, C-style casts make code brittle and difficult to search for high-risk operations. **The use of C-style casts should be avoided in Modern C++ code.**

## 13.2 `static_cast`: Compile-Time Conversions

**`static_cast`** is the C++ standard way to perform conversions that are logically safe and reversible, and which the compiler can check at **compile time**. It is the replacement for most of your C-style cast usage.

### Primary Use Cases

1.  **Standard Numerical Conversions:** `int` to `double`, `enum` to `int`, etc.
2.  **Explicit Conversions:** Using a constructor or conversion operator that was marked `explicit` (Chapter 12.3).
3.  **Upcasting:** Converting a derived class pointer/reference to its public base class pointer/reference (this is always safe).
4.  **Safe Downcasting (Non-Polymorphic):** Converting a base pointer/reference to a derived pointer/reference, but only when the programmer is **certain** of the underlying type. If the type is wrong, this results in **Undefined Behavior** (UB).

```cpp
class Base {};
class Derived : public Base {};

void demonstrate_static() {
    double d = 3.14159;

    // 1. Numerical Conversion
    int i = static_cast<int>(d); // Clear intent: truncate the double

    // 2. Upcasting (safe, usually implicit anyway)
    Derived d_obj;
    Base* b_ptr = &d_obj;

    // 3. Downcasting (Unsafe if type is unknown/non-polymorphic)
    Derived* d_ptr = static_cast<Derived*>(b_ptr);
}
```

`static_cast` is typically the fastest cast, as it involves no run-time checking.

## 13.3 `dynamic_cast`: Run-Time Polymorphic Checking

**`dynamic_cast`** is the tool for performing safe **downcasting** (Base $\to$ Derived) and cross-casting in complex inheritance hierarchies. Unlike `static_cast`, `dynamic_cast` performs a safety check at **run time**.

### Requirement: Polymorphism

`dynamic_cast` can only be used on classes that are **polymorphic** (i.e., the class must have at least one **virtual function**, Chapter 11.2). The presence of a vtable is what enables the run-time type check (RTTI).

### Failure Modes

When the run-time check finds that the pointer/reference does not actually point to the target type, the failure mode depends on whether a pointer or a reference is being cast:

| Casting Type         | Failure Result                            | C# Analogy               |
| :------------------- | :---------------------------------------- | :----------------------- |
| **Pointer** (`T*`)   | Returns **`nullptr`**.                    | The `as` operator.       |
| **Reference** (`T&`) | Throws an exception: **`std::bad_cast`**. | A direct, throwing cast. |

```cpp
class ILoggable { public: virtual ~ILoggable() = default; }; // Polymorphic base
class LoggerA : public ILoggable {};
class LoggerB : public ILoggable {};

void process_logger(ILoggable* base_ptr) {
    // Attempt safe downcasting via pointer
    LoggerA* ptr_a = dynamic_cast<LoggerA*>(base_ptr);

    if (ptr_a) {
        // Successful cast: use LoggerA methods
        std::cout << "Successfully cast to LoggerA.\n";
    } else {
        // Failed cast: the pointer was pointing to a LoggerB object, not a LoggerA
        std::cout << "Cast failed, pointer is nullptr.\n";
    }
}
```

**Best Practice:** Prefer the pointer form of `dynamic_cast` and check for `nullptr`, as exceptions are expensive.

## 13.4 `const_cast` and `reinterpret_cast`: High-Risk Operations

These two casts are specialized, rarely used, and often signal a design flaw or dangerously low-level manipulation.

### `const_cast`

**`const_cast`** is the _only_ C++ cast that can add or remove the `const` or `volatile` qualifiers from an object or pointer.

- **Primary Use Case:** Interfacing with a legacy C library or API that was not properly `const`-correct (i.e., takes a `char*` but doesn't actually modify the data).
- **Safety Rule:** You can only safely remove `const` from an object that was **not originally declared `const`**. If you remove `const` and attempt to modify an object that was truly declared `const`, the result is **Undefined Behavior**.

```cpp
void legacy_c_func(char* data); // Takes non-const pointer

void caller(const char* data) {
    // OK, assuming legacy_c_func doesn't actually write to data
    legacy_c_func(const_cast<char*>(data));
}
```

### `reinterpret_cast`

**`reinterpret_cast`** is the most dangerous cast. It performs a direct, bitwise reinterpretation of the underlying binary pattern, typically converting one pointer type to a completely unrelated pointer type, or a pointer to an integer.

- **High Risk:** It provides no safety check and often violates alignment and type rules. It is non-portable.
- **Primary Use Case:** Low-level, system-specific code like memory mapping, driver development, or specific network serialization when working with raw memory buffers.

```cpp
int i = 65;
// Extremely dangerous: treat the 4 bytes of 'i' as a pointer to a char
char* c_ptr = reinterpret_cast<char*>(&i);
// *c_ptr might point to the least significant byte of i (value 65, ASCII 'A')
```

**NEVER** use `reinterpret_cast` unless you are absolutely certain of the memory layout and context.

## 13.5 Run-Time Type Information (RTTI) and `typeid`

**Run-Time Type Information (RTTI)** is the C++ mechanism that allows a program to discover the actual type of an object during execution. It is the underlying facility that makes **`dynamic_cast`** possible.

### The `typeid` Operator

The `typeid` operator is the simplest way to access RTTI. It returns a reference to a **`std::type_info`** object, which holds information about the type.

- **Syntax:** `typeid(expression)` or `typeid(type-name)`
- **Primary Use:** Comparing the run-time type of an object to a known type.

```cpp
#include <typeinfo>
#include <iostream>

class Base {};
class Derived : public Base {};

void check_type(Base* b_ptr) {
    if (typeid(*b_ptr) == typeid(Derived)) {
        std::cout << "The object is actually a Derived type.\n";
    } else if (typeid(*b_ptr) == typeid(Base)) {
        std::cout << "The object is a Base type.\n";
    }
}
```

**Note on Polymorphism:** When applied to a pointer or reference to a **polymorphic** type (Base class has a virtual function), `typeid` correctly returns the run-time type of the object pointed to. If applied to a non-polymorphic type, it returns the static (declared) type of the expression.

**Trade-offs:** RTTI is typically enabled by default, but some development environments (especially embedded or high-performance game engines) disable it to save memory space and reduce the minimal overhead of dynamic dispatch and type information storage. If RTTI is disabled, `dynamic_cast` and `typeid` cannot be used.

## Key Takeaways

- **Avoid C-Style Casts:** The C-style cast (`(T)expr`) is ambiguous and dangerous. Always use the named, specific C++ casting operators.
- **`static_cast` is the Default:** Use **`static_cast`** for safe, verifiable, compile-time conversions (numeric, explicit constructors, upcasting).
- **`dynamic_cast` is for Polymorphism:** Use **`dynamic_cast`** for safe **downcasting** in class hierarchies that have at least one **virtual function**. Use the pointer form and check for `nullptr` on failure.
- **High-Risk Operations:** **`const_cast`** is only for removing `const` (usually for legacy APIs). **`reinterpret_cast`** is for low-level, bitwise reinterpretation and should be avoided.
- **RTTI and `typeid`:** **RTTI** (Run-Time Type Information) enables `dynamic_cast`. The **`typeid`** operator allows you to query the run-time type of a polymorphic object for comparison.

### Exercises

1.  **C-Style Cast Ambiguity:** Write a function that takes a `const char*` pointer. In `main`, call this function with a literal string `"test"`.

    - _Task:_ Use a **C-style cast** to pass the string literal into the function. Explain which of the four C++ casts the C-style cast is implicitly using. Now, replace the C-style cast with the correct specialized C++ cast.
    - _Hint:_ String literals are `const char[]`, so they decay to `const char*`. The C-style cast must remove `const`.

2.  **Safe Polymorphic Downcasting:** Create a base class `Document` with a virtual destructor. Create a derived class `PDFDocument`. Create a `Document*` that points to a `PDFDocument` object.

    - _Task:_ Use **`dynamic_cast`** to safely cast the `Document*` to a `PDFDocument*`. Check the result for `nullptr`. Then, try to cast to a non-existent `HTMLDocument*` type to demonstrate the `nullptr` failure mode.
    - _Hint:_ The `dynamic_cast` ensures the safety that `static_cast` cannot provide at runtime.

3.  **Static vs. Dynamic Downcasting:** Take the code from Exercise 2. This time, create a **non-polymorphic** base class `NonPolyBase` (no virtual functions). Attempt to use `dynamic_cast` on a pointer of this type.

    - _Task:_ Observe the compile-time error. Explain why `dynamic_cast` fails to compile for non-polymorphic types, and what mechanism is missing.
    - _Hint:_ `dynamic_cast` relies on RTTI, which requires a vtable, which is only generated when a class is polymorphic.

4.  **RTTI and Type Comparison:** Using the `Document` and `PDFDocument` classes (which must be polymorphic), write a function that takes a `Document*`.
    - _Task:_ Inside the function, use **`typeid`** to check if the run-time type of the object is exactly `PDFDocument`. Print the result of `typeid(*ptr).name()`.
    - _Hint:_ You must dereference the pointer (`*ptr`) for `typeid` to perform a run-time check on the object's actual type.

---

## Where to go Next

- **[Part I:](./part1.md): The C++ Ecosystem and Foundation:** This section establishes the philosophical and technical underpinnings of C++, focusing on compilation, linking, and the modern modularization system.
- **[Part II](./part2.md): Core Constructs, Classes, and Basic I/O:** Here, we cover the essential C++ syntax, focusing on differences in data types, scoping, **`const` correctness**, and the function of **lvalue references**.
- **[Part III](./part3.md): The C++ Memory Model and Resource Management:** The most critical section, which deeply explores raw pointers, value categories, **move semantics**, and the indispensable role of **smart pointers** and the \*\*RAII\*\* idiom.
- **[Part IV](./part4.md): Classical OOP, Safety, and Type Manipulation:** This part addresses familiar object-oriented concepts like **inheritance** and **polymorphism**, emphasizing C++'s rules for \*\*exception safety\*\* and type-safe casting.
- **[Part V](./part5.md): Genericity, Modern Idioms, and The Standard Library:** Finally, we explore the advanced capabilities of **templates**, **C++20 Concepts**, **lambda expressions**, and the power of the **Standard Library containers** and \*\*Ranges\*\* for highly generic and expressive code.
- **[Appendix](./appendix.md):** Supplementary materials including coding style guidelines, compiler flags, and further reading.
