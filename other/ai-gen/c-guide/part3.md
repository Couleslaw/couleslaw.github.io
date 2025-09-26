---
layout: default
title: C Language Basics Part III | Jakub Smolik
---

[..](./index.md)

# Part III: Advanced Data and Operations

Part III builds upon your foundational C knowledge, introducing you to more complex data types and operations. You'll learn how to create custom data structures using `struct` and `union`, refine your types with `const`, `enum`, and `typedef`, and manipulate data at the bit level with bitwise operations. This section emphasizes the differences between C's low-level capabilities and C#'s higher-level abstractions, preparing you for advanced programming tasks in C.

## Table of Contents

#### [7. Aggregate Data Types (creating custom structures)](#7-aggregate-data-types-creating-custom-structures-1)

- **7.1. `struct`: Defining Custom Types:** How to group related data of different types into a single, custom data structure.
- **7.2. C `struct` vs. C# `struct`/`class` (Value vs. Reference Semantics):** A crucial comparison that clarifies how C's `struct` behaves as a value type, unlike C# classes.
- **7.3. Pointers to Structures (`->` operator):** How to access members of a structure through a pointer using the `->` operator.
- **7.4. `union` Sharing Memory for Different Types:** An introduction to a unique data type that allows different members to occupy the same memory location.
- **7.5. Bit Fields for Memory Optimization:** A technique for packing multiple binary flags or small integer values into a single memory byte.

#### [8. Advanced Type System Features (refining your types)](#8-advanced-type-system-features-refining-your-types-1)

- **8.1. `const` for Read-Only Data:** Using the `const` keyword to declare constants and enforce immutability for variables and pointers.
- **8.2. `enum` for Named Constants:** Creating named integer constants for improved code readability and maintainability.
- **8.3. `typedef` for Creating Type Aliases:** How to create a new, more descriptive name for an existing data type.
- **8.4. Reading Complex Declarations (cdecl):** A practical guide and tool recommendations for deciphering convoluted C type declarations.

#### [9. Bitwise Operations (low-level data manipulation)](#9-bitwise-operations-low-level-data-manipulation-1)

- **9.1. Bitwise Operators: `&`, `|`, `^`, `~`:** An explanation of how to perform logical operations directly on the individual bits of an integer.
- **9.2. Bit-Shifting Operators: `<<`, `>>`:** How to shift bits to the left or right, a common operation for multiplication/division and low-level communication.
- **9.3. Common Use Cases: Flags and Masks:** Practical examples of how bitwise operations are used to manage binary flags and extract specific data.

---

## 7. Aggregate Data Types (creating custom structures)

In C#, you can group related data into a single unit using `struct` or `class`. In C, the primary way to do this is with a `struct`. While the name is the same, the behavior and semantics of a C `struct` are fundamentally different from a C# `class` and even subtly different from a C# `struct`. This chapter will clarify how to define and use C `struct`s, introduce the `->` operator for pointer-based access, and explore two advanced data types: `union` and **bit fields** for low-level memory control.

## 7.1. `struct`: Defining Custom Types

A **`struct`** (short for structure) is a C data type that allows you to bundle different types of variables together under a single name. It's the C equivalent of a data-only class or a C# `record`.

To define a `struct`, you use the `struct` keyword, followed by a tag name and a list of member variables.

```c
#include <stdio.h>

// 1. Define the struct
struct Point {
    int x;
    int y;
};

int main() {
    // 2. Declare a variable of type 'struct Point'
    struct Point p1;

    // 3. Access members using the dot (.) operator
    p1.x = 10;
    p1.y = 20;

    printf("p1 coordinates: (%d, %d)\n", p1.x, p1.y);

    return 0;
}
```

A common C idiom is to use `typedef` to create a more convenient alias for the `struct` type, allowing you to omit the `struct` keyword every time you declare a variable.

```c
#include <stdio.h>

// Use typedef to create an alias named 'Point'
typedef struct {
    int x;
    int y;
} Point; // The alias is named Point

int main() {
    Point p1; // Now you can simply use 'Point'
    p1.x = 10;
    p1.y = 20;

    printf("p1 coordinates: (%d, %d)\n", p1.x, p1.y);

    return 0;
}
```

## 7.2. C `struct` vs. C# `struct`/`class` (Value vs. Reference Semantics)

This is a critical distinction. In C, a `struct` is a **value type**. When you pass a `struct` to a function or assign it to another variable, a **complete copy** of the entire structure is made. This is in direct contrast to C# `class`es, which are **reference types** and are always passed by reference. C# `struct`s are also value types, but the behavior of C `struct`s is simpler, with no concept of a boxed type or object headers.

Consider the following example:

```c
#include <stdio.h>

typedef struct {
    int x;
    int y;
} Point;

// This function receives a COPY of the Point struct
void move_point(Point p, int dx, int dy) {
    p.x += dx;
    p.y += dy;
    printf("Inside function: (%d, %d)\n", p.x, p.y);
}

int main() {
    Point my_point = {10, 20};
    printf("Before function call: (%d, %d)\n", my_point.x, my_point.y);

    move_point(my_point, 5, 5);

    // The original struct is UNCHANGED
    printf("After function call:  (%d, %d)\n", my_point.x, my_point.y);

    return 0;
}
```

**Output:**

```
Before function call: (10, 20)
Inside function: (15, 25)
After function call:  (10, 20)
```

To achieve C#'s reference semantics (where the original object is modified), you must pass a **pointer to the `struct`**, just as you would with any other data type.

## 7.3. Pointers to Structures (`->` operator)

When you have a pointer to a `struct`, you cannot use the `.` operator to access its members. You must first dereference the pointer and then access the member. C provides a convenient shortcut for this common operation: the **arrow operator (`->`)**.

The expression `p->x` is syntactic sugar for `(*p).x`.

```c
#include <stdio.h>

typedef struct {
    int x;
    int y;
} Point;

// This function receives a POINTER to the Point struct
void move_point_by_pointer(Point* p, int dx, int dy) {
    p->x += dx; // Use the arrow operator for convenience
    p->y += dy;
    // This is equivalent to: (*p).x += dx;
}

int main() {
    Point my_point = {10, 20};
    printf("Before function call: (%d, %d)\n", my_point.x, my_point.y);

    // Pass the address of the struct
    move_point_by_pointer(&my_point, 5, 5);

    // The original struct IS MODIFIED
    printf("After function call:  (%d, %d)\n", my_point.x, my_point.y);

    return 0;
}
```

## 7.4. `union`: Sharing Memory for Different Types

A **`union`** is a special data type that allows different members to occupy the **same memory location**. The `union`'s size is equal to the size of its largest member. It provides a way to conserve memory when you know that only one of a set of members will be used at any given time.

A classic use case is when a value could be of one type or another.

```c
#include <stdio.h>

// A union that can hold either an integer or a float
union Value {
    int i;
    float f;
};

int main() {
    union Value val;

    val.i = 100;
    printf("Integer member: %d\n", val.i);

    val.f = 3.14; // The memory is now overwritten with a float
    printf("Float member: %f\n", val.f);

    // Reading from the integer member is now undefined behavior!
    printf("Integer member after float write: %d\n", val.i);

    printf("Size of union: %zu bytes\n", sizeof(union Value));

    return 0;
}
```

As you can see, after assigning a value to `val.f`, the value of `val.i` is meaningless, as the memory has been reinterpreted. Using a `union` requires careful tracking of which member is currently "active," often with a separate `enum` or `int` field.

## 7.5. Bit Fields for Memory Optimization

**Bit-fields** let you pack several small integer values into a single machine word (or a few words) by specifying the exact number of bits each occupies. They’re useful when you need to match a hardware register layout, store many small flags efficiently, or communicate using binary protocols.

```c
#include <stdio.h>

// A struct to store file permissions
// Using bit fields to pack 4 booleans into a single byte
struct FilePermissions {
    unsigned int read    : 1; // 1 bit
    unsigned int write   : 1; // 1 bit
    unsigned int execute : 1; // 1 bit
    unsigned int hidden  : 1; // 1 bit
};

int main() {
    struct FilePermissions file_flags;

    file_flags.read = 1;
    file_flags.write = 0;
    file_flags.execute = 1;
    file_flags.hidden = 0;

    printf("Read flag: %d\n", file_flags.read);
    printf("Total size: %zu bytes\n", sizeof(struct FilePermissions));

    return 0;
}
```

The `sizeof` operator will likely report `4` bytes, demonstrating that the compiler has packed the four 1-bit flags into a single word of memory.

## Key Takeaways

- **`struct`s are C's custom data types.** They group related data but do not have methods or properties like a C# class.
- **C `struct`s are value types.** When passed to a function, a copy is made. To modify the original, you must pass a **pointer**.
- **Use the `->` operator** to access members of a `struct` through a pointer (`p->member` is shorthand for `(*p).member`).
- **`union`s allow members to share the same memory.** They are used for memory optimization but require careful use to avoid reading from the wrong member.
- **Bit fields** allow you to specify the size of a `struct` member in bits, a powerful technique for packing data and saving memory.

### Exercises

1.  **Person Struct:** Define a `struct` named `Person` with members for `char* name` and `int age`. In `main`, declare a `Person` variable, initialize its members, and then print them to the console.

    - _Hint:_ For the `name` member, you can use a string literal like `person1.name = "Alice";`.

2.  **Move a Rectangle:** Define a `Point` struct (from the chapter). Then define a `struct Rectangle` with two `Point` members, `top_left` and `bottom_right`. Write a function `void move_rectangle(Rectangle* rect, int dx, int dy)` that takes a pointer to a `Rectangle` and moves it by `dx` and `dy` on the coordinate plane. Demonstrate the function's success by printing the rectangle's coordinates before and after the call.

    - _Hint:_ You'll need to pass the address of your `Rectangle` to the function: `move_rectangle(&my_rect, 10, 10);`.

3.  **Union as a Data Carrier:** Create a `union` that can hold either an `int` or a `float`. Write a program that asks the user to enter a value. If the user enters a number with a decimal point, store it as a `float`. If not, store it as an `int`. Print the value. This will require you to use a separate variable to track which type is currently stored.
    - _Hint:_ This exercise is tricky. You could read the input as a string and check for the presence of a `.` character before converting it to an `int` or a `float`.

---

## 8. Advanced Type System Features (refining your types)

C's type system is often seen as a bare-bones tool for telling the compiler how to interpret bits in memory. However, the language provides several keywords and features that allow you to refine these types, enforce constraints, improve code readability, and provide self-documenting code. This chapter explores three such features—`const`, `enum`, and `typedef`—and then provides a practical guide to reading the often-intimidating complex C type declarations.

---

## 8.1. `const` for Read-Only Data

The `const` keyword is a type qualifier that tells the compiler that a variable's value should not be changed after its initialization. It's a tool for enforcing immutability and is crucial for writing safe, robust C code. While C# has `const` for compile-time constants and `readonly` for instance members, C's `const` is more versatile.

The placement of `const` is critical and can be a source of confusion. A simple rule of thumb is to read the declaration from right to left, and `const` applies to the variable or pointer to its immediate right.

#### `const` and Pointers

1.  **Pointer to a constant:** `const int* ptr_to_const;`

    - Here, `const` modifies the `int` to its right. `ptr_to_const` is a pointer to an integer whose value is constant.
    - You **cannot** change the value that `ptr_to_const` points to (`*ptr_to_const = 20;` is illegal).
    - You **can** change the pointer itself to point to a different location (`ptr_to_const = &another_int;` is legal).

2.  **Constant pointer:** `int* const const_ptr;`

    - Here, `const` modifies `const_ptr`. `const_ptr` is a constant pointer to an integer.
    - You **can** change the value the pointer points to (`*const_ptr = 20;` is legal).
    - You **cannot** change the pointer itself to point to a different location (`const_ptr = &another_int;` is illegal).

3.  **Constant pointer to a constant:** `const int* const everything_is_const;`
    - This is a constant pointer to a constant integer. You cannot change the value it points to, and you cannot change where it points.

Using `const` in function parameters is a key best practice. It serves as a contract, guaranteeing to the caller that the function will not modify the data passed in.

```c
#include <stdio.h>

// This function guarantees it will not modify the data pointed to by 'data'
void print_read_only_array(const int* data, int size) {
    for (int i = 0; i < size; i++) {
        printf("%d ", data[i]);
    }
    printf("\n");
    // data[0] = 99; // Error: cannot modify a const int
}

int main() {
    int my_array[] = {1, 2, 3, 4, 5};
    print_read_only_array(my_array, 5);
    return 0;
}
```

---

## 8.2. `enum` for Named Constants

In C, an **`enum`** (short for enumeration) defines a set of named integer constants. It makes your code more readable and self-documenting than using raw numbers.

```c
#include <stdio.h>

// A simple enum
enum DayOfWeek {
    Sunday,    // 0 by default
    Monday,    // 1
    Tuesday,   // 2
    Wednesday, // 3
    Thursday,  // 4
    Friday,    // 5
    Saturday   // 6
};

int main() {
    enum DayOfWeek today = Wednesday;
    printf("Today is day number: %d\n", today); // Prints 3

    // You can also assign explicit values
    enum Status {
        SUCCESS = 0,
        PENDING = 1,
        ERROR = 99
    };

    enum Status current_status = ERROR;
    printf("Current status code: %d\n", current_status); // Prints 99

    return 0;
}
```

A key difference from C# is that C `enum`s are **not type-safe**. They are simply aliases for integers, and the compiler will not prevent you from assigning an integer value that is not one of the named constants. For example, `enum DayOfWeek some_day = 10;` is perfectly valid C code, even though `10` is not a `DayOfWeek`.

---

## 8.3. `typedef` for Creating Type Aliases

The **`typedef`** keyword allows you to create a new name for an existing data type. This is particularly useful for simplifying complex type declarations, making your code easier to read and more portable.

```c
#include <stdio.h>

// Alias for unsigned long long
typedef unsigned long long BigInt;

// Alias for a pointer to a character (a C-style string)
typedef char* String;

int main() {
    BigInt large_number = 1234567890123456789ULL;
    printf("BigInt value: %llu\n", large_number);

    String my_name = "Alice";
    printf("My name is: %s\n", my_name);

    return 0;
}
```

As we saw in Chapter 7, `typedef` is most commonly used with `struct`s to create a cleaner syntax. It's also essential for function pointers and other complex types.

---

## 8.4. Reading Complex Declarations (cdecl)

C's type declarations can be notoriously difficult to parse. A declaration like `int (*(*fp)[10])(void);` can be a headache. A simple technique to decipher these is the **right-left rule**:

1.  Start with the variable name.
2.  Look to the right and follow the parentheses `()`, brackets `[]`, and other operators.
3.  When you hit a closing bracket or parenthesis, go left.
4.  Repeat the process until you've read the entire declaration.

Let's apply this to a complex example: `char *(*c[10])(int **);`

1.  **`c`**: Start with the variable name.
2.  Go right: `c[10]` - `c` is an **array of 10**.
3.  Go left: `(*c[10])` - an array of 10 **pointers**.
4.  Go right: `(*c[10])(int **)` - an array of 10 pointers to **functions that take a pointer to a pointer to an integer** (`int **`).
5.  Go left again: `*(*c[10])(int **)` - and **return a pointer**.
6.  Go left one more time: `char *(*c[10])(int **)` - to a **character**.

So, `c` is "an array of 10 pointers to functions that take a pointer to a pointer to an integer and return a pointer to a character."

To check your work and simplify your life, you can use the online tool **[cdecl](https://cdecl.org/)**. It translates C declarations to English.

`cdecl > declare c as array 10 of pointer to function (pointer to pointer to int) returning pointer to char`
`char *(*c[10])(int **)`

`cdecl > char *(*(*fp)[10])(void);`
`declare fp as pointer to array 10 of pointer to function (void) returning pointer to char`

## Key Takeaways

- **`const` enforces immutability** and is a powerful tool for safety and self-documentation. Be mindful of its placement with pointers.
- **`enum`s create named integer constants** for readability, but they are not type-safe like in C#.
- **`typedef` creates aliases** for existing types, simplifying complex declarations and improving code portability.
- **Complex declarations can be read with the right-left rule.** The `cdecl` utility is an invaluable tool for understanding them.

### Exercises

1.  **Const Pointer Challenge:** Write a function `void increment_value(const int* const ptr)` that attempts to increment the value pointed to by `ptr` and then attempts to make `ptr` point to another variable. Compile and observe the compiler errors. Then, change the function signature to `void increment_value(int* ptr)` and modify the value correctly.

    - _Hint:_ The first signature should produce two distinct compiler errors.

2.  **Enum for Months:** Define an `enum` for the months of the year, starting with `January = 1`. Write a program that takes an integer from the command line (from 1 to 12) and prints the corresponding month's name using the `enum`.

    - _Hint:_ You can use a `switch` statement or an array of strings to map the enum values to names.

3.  **Typedef for Function Pointers:** (This is an advanced exercise that introduces function pointers early.) The `bsearch` function in `<stdlib.h>` takes a function pointer as an argument. Use `typedef` to create a type alias for the comparison function signature: `typedef int (*CompareFunc)(const void*, const void*);`. Then, write a function `int compare_ints(const void* a, const void* b)` that implements the comparison logic. This will make your call to `bsearch` cleaner.
    - _Hint:_ The `compare_ints` function must cast its `void*` arguments to `int*` to perform the comparison.

---

## 9. Bitwise Operations (low-level data manipulation)

In C#, you can use bitwise operators to work with flags or packed data, but in C, this is a much more common and essential part of a programmer's toolkit. Bitwise operations are not performed on the decimal value of a number, but on its underlying binary representation. They provide a powerful and efficient way to manipulate data at the lowest possible level.

This chapter will introduce C's bitwise and bit-shifting operators, explain their behavior with simple binary examples, and demonstrate their most common real-world use cases.

## 9.1. Bitwise Operators: `&`, `|`, `^`, `~`

Bitwise operators perform logical operations on each corresponding pair of bits in an integer.

- **Bitwise AND (`&`):** A `1` is set in the result if and only if both bits are `1`.

  ```
    0011 0011  (51)
  & 0000 1111  (15)
  = 0000 0011  (3)
  ```

- **Bitwise OR (`|`):** A `1` is set in the result if at least one of the bits is `1`.

  ```
    0011 0011  (51)
  | 0000 1111  (15)
  = 0011 1111  (63)
  ```

- **Bitwise XOR (`^`):** A `1` is set in the result if the bits are different.

  ```
    0011 0011  (51)
  ^ 0000 1111  (15)
  = 0011 1100  (60)
  ```

- **Bitwise NOT (`~`):** A unary operator that flips every bit. `1` becomes `0`, and `0` becomes `1`. This is also known as the **one's complement**.

  ```c
  #include <stdio.h>

  int main() {
      unsigned char a = 0b00000010; // 2 in binary
      unsigned char b = ~a;        // Flips all bits
      printf("~2 is: %u\n", b);    // Prints 253
      return 0;
  }
  ```

  For a signed integer, the result of a bitwise NOT can be a surprising negative number due to two's complement representation.

## 9.2. Bit-Shifting Operators: `<<`, `>>`

Bit-shifting operators move the bits of an integer to the left or right.

- **Left Shift (`<<`):** Shifts the bits to the left, filling the new rightmost bits with `0`. This is equivalent to multiplying by a power of 2.

  ```
    0000 0010  (2)
  << 2
  = 0000 1000  (8)
  ```

- **Right Shift (`>>`):** Shifts the bits to the right. The behavior of the sign bit on signed integers is **implementation-defined** and can vary between compilers. For this reason, it is best to use `unsigned` types for bitwise operations where you do not need a sign. Right shifting is equivalent to integer division by a power of 2.
  ```
    0000 1000  (8)
  >> 2
  = 0000 0010  (2)
  ```

## 9.3. Common Use Cases: Flags and Masks

Bitwise operations are a staple of C programming, especially for low-level data management. Two of the most common patterns are using bits as flags and using a "mask" to isolate specific bits.

#### Flags: Storing Binary States

This pattern is used to store multiple boolean-like states in a single integer, saving memory and providing an efficient way to check and set permissions.

1.  **Define the Flags:** Use an `enum` with powers of 2.

    ```c
    enum FileAccess {
        READ    = 1 << 0,  // 0001
        WRITE   = 1 << 1,  // 0010
        EXECUTE = 1 << 2,  // 0100
        HIDDEN  = 1 << 3   // 1000
    };
    ```

2.  **Combine Flags:** Use the `|` operator to turn multiple flags on.

    ```c
    int user_permissions = READ | WRITE; // 0011
    ```

3.  **Check for a Flag:** Use the `&` operator to check if a specific flag is set. The expression `(flags & FLAG) == FLAG` is a reliable way to check for a specific bit.

    ```c
    if (user_permissions & WRITE) {
        printf("User has write access.\n");
    }
    ```

4.  **Clear a Flag:** Use `&` and `~` to turn a flag off.
    ```c
    user_permissions &= ~WRITE; // Turn off the WRITE bit
    ```

#### Masks: Extracting Specific Data

A **mask** is a value that, when used with a bitwise operator, allows you to isolate a specific group of bits from a larger number. This is common in network protocols, file formats, and color representation.

A common example is extracting the red, green, and blue components from a 32-bit color integer.

```c
#include <stdio.h>

int main() {
    // A 32-bit color: 0xAARRGGBB
    // A=Alpha, R=Red, G=Green, B=Blue
    unsigned int color = 0xCD2A3B4C;

    // Extracting the components using masks and shifts
    unsigned int alpha = (color >> 24) & 0xFF;
    unsigned int red   = (color >> 16) & 0xFF;
    unsigned int green = (color >> 8)  & 0xFF;
    unsigned int blue  = color & 0xFF;

    printf("Alpha: %X\n", alpha);
    printf("Red:   %X\n", red);
    printf("Green: %X\n", green);
    printf("Blue:  %X\n", blue);

    return 0;
}
```

The shift operator moves the desired component to the lowest 8 bits, and the mask `0xFF` (`1111 1111` in binary) isolates those bits, zeroing out everything else.

## Key Takeaways

- **Bitwise operators work on binary representations.** Think of each bit as a tiny light switch that is either on (`1`) or off (`0`).
- **Bitwise operators are efficient.** They are often faster than their arithmetic equivalents.
- **The behavior of `>>` on signed integers is undefined.** Always use `unsigned` integer types for bitwise operations where you need predictable behavior.
- **Use bitwise operators for flags and masks.** This is a fundamental C pattern for storing and manipulating multiple boolean states or packed data in a single integer.

### Exercises

1.  **Set and Clear Flags:** Define an `enum` for user permissions: `IS_ADMIN`, `CAN_POST`, `CAN_DELETE`. Write a program that defines an `int` for a user's permissions, sets the `IS_ADMIN` and `CAN_POST` flags, then prints a message for each flag to confirm they are set. Then, clear the `CAN_POST` flag and re-check it.

    - _Hint:_ Use `|` for setting, `&` for checking, and `&` with `~` for clearing.

2.  **Toggle a Bit:** Write a program that defines an integer `flags` and a variable `TOGGLE_BIT` which is `1 << 3`. Use the XOR operator (`^`) to toggle the `TOGGLE_BIT` in `flags`. Print the binary representation of `flags` before and after the toggle to confirm the bit has flipped.

    - _Hint:_ The bitwise XOR operator is perfect for this task.

3.  **RGB to Hex:** Write a function `unsigned int rgb_to_hex(unsigned char r, unsigned char g, unsigned char b)` that takes three 8-bit color components and returns a single 32-bit unsigned integer in the `0x00RRGGBB` format.
    - _Hint:_ You'll need to use the bit-shifting and bitwise OR operators.

---

## Where to Go Next

- **[Part I](./part1.md): C Fundamentals:** Setting up your environment, writing your first C program, and understanding the basic syntax and semantics of C.
- **[Part II](./part2.md): Mastering Memory and Pointers:** Diving deep into pointers, arrays, C strings, and dynamic memory allocation, with a focus on how these concepts differ from C#.
- **[Part IV](./part4.md): Practical Tooling and Resources:** Learning how to debug C programs, use common tools, and find further resources for continued learning.
