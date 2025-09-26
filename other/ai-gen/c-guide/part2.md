---
layout: default
title: C Language Basics Part II| Jakub Smolik
---

[..](./index.md)

# Part II: Mastering Memory and Pointers

Part II delves into the core of C programming: memory management. You'll gain a deep understanding of pointers, arrays, and C strings, along with the intricacies of dynamic memory allocation. This section emphasizes the differences between C's manual memory handling and C#'s garbage-collected environment, equipping you with the skills to manage memory safely and efficiently in C.

## Table of Contents

#### [4. Pointers & Arrays (the heart of C memory management)](#4-pointers--arrays-the-heart-of-c-memory-management-1)

- **4.1. Understanding Memory Addresses:** Conceptualizing memory as a sequence of bytes, each with a unique address.
- **4.2. Pointers: Referencing (`&`) and Dereferencing (`*`):** A hands-on introduction to pointer variables and the fundamental operators used to manipulate them.
- **4.3. Pointer Arithmetic:** An explanation of how to perform arithmetic operations on pointers and their behavior in relation to data types.
- **4.4. Arrays: Declaration and Initialization:** The basics of declaring and populating contiguous blocks of memory with values.
- **4.5. The Array-Pointer Relationship:** Exploring the critical concept that array names in C often decay into pointers to their first element.
- **4.6. Null Pointers vs. C# `null`:** A comparison of C's `NULL` macro and its role in indicating an invalid pointer, contrasted with C#'s reference-type null.

#### [5. C Strings (working with text)](#5-c-strings-working-with-text-1)

- **5.1. NUL-Terminated Character Arrays:** Understanding the core principle of C strings as arrays of characters terminated by a null (`\0`) character.
- **5.2. String Literals:** How constant string values are stored in memory and how to use them safely.
- **5.3. Common Pitfalls:** Buffer Overflows and Off-by-One Errors: Identifying and avoiding two of the most frequent and dangerous string-related vulnerabilities in C.
- **5.4. Standard Library String Functions (`<string.h>`):** An overview of essential functions for copying, concatenating, and comparing strings.

#### [6. Dynamic Memory Allocation (manual heap management)](#6-dynamic-memory-allocation-manual-heap-management-1)

- **6.1. The Heap vs. The Stack:** A clear distinction between these two primary memory regions, their purpose, and their management.
- **6.2. `malloc`, `calloc`, `realloc`, and `free`:** A detailed look at the four fundamental functions for manually allocating and freeing memory on the heap.
- **6.3. Manual Memory Management vs. C# Garbage Collection (GC):** A side-by-side comparison of the developer's responsibility in C with C#'s automated memory cleanup.
- **6.4. Best Practices: Checking for `NULL`, Avoiding Memory Leaks:** Essential guidelines for robust and safe dynamic memory management.

---

## 4. Pointers & Arrays (the heart of C memory management)

As a C# developer, you've spent your career interacting with memory through high-level abstractions like references and garbage collection. In C, you work directly with memory addresses via **pointers**. This is the single most significant conceptual shift you will make. Pointers are the heart of C, enabling dynamic data structures, efficient algorithms, and direct hardware interaction. This chapter will demystify pointers, their relationship to arrays, and how they give you explicit control over your program's memory.

## 4.1. Understanding Memory Addresses

Before we dive into pointers, let's establish a mental model of your computer's memory. Think of it as a vast, ordered sequence of storage units, or **bytes**. Each byte has a unique numerical identifier called a **memory address**.

Imagine your memory as a street with houses. Each house is a byte, and its address is a unique street number. When you declare a variable, the compiler finds an empty house (or a set of contiguous houses) and gives it a name, like `my_variable`.

```c
int my_variable = 10;
```

When this line of code runs, the value `10` is stored in a location in memory, say, at address `0x7FFC...`.

## 4.2. Pointers: Referencing (`&`) and Dereferencing (`*`)

A **pointer** is simply a variable whose value is a memory address. It "points" to another variable. Just like an `int` pointer in C# is a `ref int`, a C pointer is a variable that holds a memory address.

There are two fundamental operators for working with pointers:

- **The Address-Of Operator (`&`):** A unary operator that returns the memory address of its operand.
- **The Dereference Operator (`*`):** A unary operator that gives you the value stored at the memory address the pointer is pointing to. Think of it as "following the pointer."

Let's see them in action.

```c
#include <stdio.h>

int main() {
    int value = 42;
    int* ptr; // Declaration: a pointer to an integer

    ptr = &value; // Referencing: ptr now holds the address of 'value'

    printf("The value is: %d\n", value);
    printf("The address of 'value' is: %p\n", &value);
    printf("The pointer 'ptr' stores the address: %p\n", ptr);
    printf("The value at the address in 'ptr' is: %d\n", *ptr); // Dereferencing

    // You can also use a pointer to modify the original variable
    *ptr = 100;
    printf("The new value is: %d\n", value);

    return 0;
}
```

> **Note:** The `%p` format specifier is used to print a pointer's value (a memory address).

In C#, this is conceptually similar to `ref` or `out` parameters, but with pointers, this is a core language feature, not just a keyword for function signatures.

## 4.3. Pointer Arithmetic

This is where C's flexibility truly shows. Arithmetic operations on a pointer are not byte-based but **type-based**. When you increment a pointer, it moves forward not by one byte, but by the size of the data type it points to.

Consider an `int*` pointer. On most 64-bit systems, `sizeof(int)` is 4 bytes. If `ptr` points to address `0x1000`, then `ptr + 1` will point to `0x1004`, not `0x1001`. This automatic scaling is a powerful feature that makes iterating over arrays much cleaner.

```c
#include <stdio.h>

int main() {
    int numbers[] = {10, 20, 30, 40};
    int* ptr = &numbers[0]; // ptr points to the first element

    // ptr + 1 advances the pointer by sizeof(int) bytes
    printf("Value at ptr: %d\n", *ptr);
    printf("Value at ptr + 1: %d\n", *(ptr + 1));
    printf("Value at ptr + 2: %d\n", *(ptr + 2));

    // You can also use increment/decrement operators
    ptr++;
    printf("Value at new ptr location: %d\n", *ptr);

    return 0;
}
```

## 4.4. Arrays: Declaration and Initialization

An **array** in C is a contiguous block of memory that holds a fixed number of elements of the same data type.

```c
#include <stdio.h>

int main() {
    // Declaration with explicit size and initialization
    int scores[5] = {98, 87, 92, 75, 84};

    // Accessing elements using the index operator
    printf("Second score is: %d\n", scores[1]);

    // Modifying an element
    scores[1] = 90;
    printf("New second score is: %d\n", scores[1]);

    // Declaration with size inferred from initializer list
    double temperatures[] = {25.5, 26.1, 24.8};

    return 0;
}
```

Array access in C is similar to C# with the `[]` operator, but it's important to remember that there are **no bounds checks**. Accessing `scores[100]` will not throw an `IndexOutOfRangeException`; it will simply read from or write to an invalid memory location, leading to unpredictable behavior or a program crash.

## 4.5. The Array-Pointer Relationship

This is a fundamental concept that connects the two topics. In C, an array name can "decay" into a pointer to its first element. This means the array name itself can be used as a pointer in many contexts.

Let's revisit our `numbers` array from earlier.

```c
#include <stdio.h>

int main() {
    int numbers[] = {10, 20, 30, 40};

    // The array name 'numbers' itself is a pointer to the first element
    int* ptr_to_first = numbers;

    // Both expressions access the same memory location and value
    printf("Value via array index: %d\n", numbers[2]);
    printf("Value via pointer arithmetic: %d\n", *(ptr_to_first + 2));

    // You can also directly use the array name for pointer arithmetic
    printf("Value using array name + 2: %d\n", *(numbers + 2));

    return 0;
}
```

This is a core C idiom. When you pass an array to a function, what's actually passed is a pointer to its first element.

A key difference between a pointer and an array name is with the `sizeof` operator.

- `sizeof(numbers)`: Returns the total size of the entire array in bytes (e.g., 4 elements \* 4 bytes/element = 16 bytes).
- `sizeof(ptr_to_first)`: Returns the size of the pointer itself (e.g., 8 bytes on a 64-bit system), as it's just a memory address.

This is a common interview question and a critical distinction to remember.

## 4.6. Null Pointers vs. C# `null`

In C#, `null` is a reference to a non-existent object. In C, `NULL` (or the integer literal `0`) is a value that a pointer can hold to indicate it is not pointing to a valid memory location.

**A `NULL` pointer is a pointer that points to address `0x0`.**

Dereferencing a `NULL` pointer is a serious error that will almost certainly cause a **segmentation fault** (a program crash). You, the C programmer, are responsible for checking if a pointer is valid before using it.

```c
#include <stdio.h>

int main() {
    int* my_ptr = NULL; // Initialize a null pointer

    if (my_ptr != NULL) {
        // This code block will not execute
        printf("The value is: %d\n", *my_ptr);
    } else {
        printf("The pointer is NULL, cannot dereference.\n");
    }

    // You can also assign a valid address later
    int a = 10;
    my_ptr = &a;

    if (my_ptr != NULL) {
        printf("The pointer is now valid, value is: %d\n", *my_ptr);
    }

    return 0;
}
```

## Key Takeaways

- **Pointers are variables that store memory addresses.** Use `&` to get an address and `*` to get the value at that address.
- **Pointer arithmetic is type-based, not byte-based.** Adding 1 to a pointer moves it forward by `sizeof(type)` bytes.
- **Arrays are contiguous memory blocks.** Accessing an array out of bounds is undefined behavior and a common source of bugs.
- **The array-pointer relationship** is a core C concept: an array name often decays into a pointer to its first element. However, they are not identical.
- **`NULL` is not `null`.** A `NULL` pointer points to memory address `0`, and dereferencing it will crash your program. Always check for `NULL` before using a pointer.

### Exercises

1.  **Pointer Swap:** Write a function `swap` that takes two integer **pointers** as arguments and swaps the values they point to. Your `main` function should declare two integer variables, print their initial values, call `swap`, and then print the new, swapped values.

    - _Hint:_ Your function signature should be `void swap(int* a, int* b)`.

2.  **Array Traversal:** Write a program that declares an array of 5 integers. Use a pointer and pointer arithmetic to loop through the array and print each element's value without using the `[]` array indexing operator.

    - _Hint:_ Use a `for` loop, a pointer `p` initialized to the start of the array, and the condition `p < array_name + size`.

3.  **Null Pointer Check:** Write a program that defines a function `print_value(int* ptr)`. This function should check if the pointer is `NULL`. If it is, it should print "Error: NULL pointer passed." Otherwise, it should print the value at the address. Call this function from `main` twice: once with a valid pointer and once with a `NULL` pointer.
    - _Hint:_ Inside the function, use an `if` statement like `if (ptr == NULL)`.

---

## 5. C Strings (working with text)

In C#, the `System.String` type is a first-class citizen of the language. It's a managed, immutable object with built-in properties for length and a host of safe methods for manipulation. In C, there is no built-in `String` type. A **C string** is simply a convention: it is an **array of characters** that is terminated by a special null character, `\0`. This fundamental difference is the source of C's power and its most common security pitfalls.

## 5.1. NUL-Terminated Character Arrays

A C string is a sequence of `char` values stored contiguously in memory, ending with the **null character (`\0`)**. The null terminator is a special character with an ASCII value of `0`. It's how C functions know where a string ends. Without it, functions would read past the end of the array, leading to a buffer overflow.

Let's look at a C string in memory.

```c
char my_string[] = "Hello!";
```

When this code is compiled, the characters `H`, `e`, `l`, `l`, `o`, `!` are stored in memory, followed immediately by a hidden `\0` character. The total size of `my_string` is **7 bytes**, not 6.

A C string can be represented by a pointer to its first character. This is why many C functions take a `char*` as an argument to represent a string.

## 5.2. String Literals

A **string literal** is a sequence of characters enclosed in double quotes (e.g., `"Hello"`). String literals are **constant** and are stored in a read-only data segment of your program's memory.

```c
#include <stdio.h>

int main() {
    char* my_literal = "Hello, World!";

    // Attempting to modify a string literal will cause a crash (segmentation fault)
    // my_literal[0] = 'J'; // DANGER: Do not do this!

    // The correct way to create a mutable string from a literal is to copy it
    char mutable_string[] = "Hello, World!";
    mutable_string[0] = 'J'; // This is safe
    printf("Mutable string: %s\n", mutable_string);

    return 0;
}
```

In the first case (`char* my_literal = "..."`), `my_literal` is a pointer to the constant string stored in the read-only section. In the second case (`char mutable_string[] = "..."`), the compiler copies the constant string from the read-only segment into a new, mutable array on the stack.

## 5.3. Common Pitfalls: Buffer Overflows and Off-by-One Errors

Since C strings have no inherent length and no built-in bounds checking, you are responsible for managing their size. This is a common source of bugs and security vulnerabilities.

A **buffer overflow** occurs when a program tries to write data beyond the boundaries of a fixed-size buffer. The `strcpy` function, for example, is notoriously unsafe because it doesn't check the size of the destination buffer.

```c
#include <stdio.h>
#include <string.h>

int main() {
    char small_buffer[10];
    char* large_string = "A very, very large string.";

    // DANGER! This will write past the end of small_buffer.
    // The program might crash, or worse, have a security vulnerability.
    strcpy(small_buffer, large_string);

    printf("Buffer content: %s\n", small_buffer);

    return 0;
}
```

A related bug is an **off-by-one error**, where a loop or operation mistakenly goes one step too far. When writing to a C string, it's easy to forget to account for the null terminator, leading to corruption of adjacent memory.

## 5.4. Standard Library String Functions (`<string.h>`)

The C Standard Library provides a set of functions for working with strings in the `<string.h>` header. You should **always use these functions** instead of writing your own, as they are optimized and battle-tested.

- `size_t strlen(const char* s);`

  - **Purpose:** Returns the length of the string `s`, **excluding** the null terminator.
  - **Note:** This is a `O(n)` operation; it has to traverse the entire string to find the `\0`. In C#, `myString.Length` is `O(1)`.

- `char* strcpy(char* dest, const char* src);`

  - **Purpose:** Copies the string from `src` to `dest`. **It is unsafe.**
  - **Recommendation:** Use `strncpy` instead to prevent buffer overflows.

- `char* strncpy(char* dest, const char* src, size_t n);`
  - **Purpose:** Copies at most `n` characters from `src` to `dest`.
  - **Important:** `strncpy` **does not guarantee a null terminator** if `src`'s length is greater than or equal to `n`. You must manually add it.

```c
#include <stdio.h>
#include <string.h>

int main() {
    char dest[20];
    const char* src = "Hello, World!";

    // Safely copy, ensuring a null terminator
    strncpy(dest, src, sizeof(dest) - 1);
    dest[sizeof(dest) - 1] = '\0'; // Manually add the null terminator

    printf("Copied string: %s\n", dest);
    printf("Length: %zu\n", strlen(dest));

    return 0;
}
```

- `char* strcat(char* dest, const char* src);`

  - **Purpose:** Appends `src` to `dest`. **It is unsafe.**
  - **Recommendation:** Use `strncat` to prevent buffer overflows.

- `int strcmp(const char* s1, const char* s2);`
  - **Purpose:** Compares two strings lexicographically.
  - **Returns:** A value less than 0 if `s1` is less than `s2`, 0 if they are equal, and a value greater than 0 if `s1` is greater than `s2`.
  - **Warning:** Unlike C#, you **cannot** use `s1 == s2` to compare the content of two C strings. This only compares the pointer addresses.

```c
#include <stdio.h>
#include <string.h>

int main() {
    char* s1 = "apple";
    char* s2 = "banana";

    // This compares the content of the strings
    if (strcmp(s1, s2) < 0) {
        printf("s1 comes before s2\n");
    }

    // DANGER: This compares pointer addresses, not string content
    if (s1 == s2) {
        printf("s1 and s2 point to the same address\n");
    }

    return 0;
}
```

## Key Takeaways

- **C strings are just `char` arrays terminated by `\0`.** There is no built-in `String` type.
- **String literals are constant.** Attempting to modify them will crash your program.
- **You are responsible for memory boundaries.** C strings have no inherent length; you must use `strlen` or track size yourself to prevent buffer overflows.
- **Always use `<string.h>` functions.** Never use `==` to compare string content; use `strcmp`.
- **Be cautious with `strcpy` and `strcat`**. Prefer the safer, length-checked versions (`strncpy` and `strncat`) to avoid common vulnerabilities.

### Exercises

1.  **String Concatenation:** Write a program that takes two strings as command-line arguments (using `argc` and `argv`). It should then create a new character array large enough to hold both strings, and use `strcat` (or `strncat` for safety) to concatenate the two into the new array. Finally, print the resulting string.

    - _Hint:_ The total size needed is `strlen(arg1) + strlen(arg2) + 1` for the null terminator.

2.  **String Comparison:** Write a program that takes two command-line arguments. Use `strcmp` to determine which string is lexicographically "less than" the other, or if they are equal. Print the result clearly (e.g., `"apple is less than banana"`).

    - _Hint:_ `strcmp` returns a negative, zero, or positive value.

3.  **Manual String Copy:** Write a simple function `my_strcpy(char* dest, const char* src)` that copies a string from `src` to `dest` **without using** the `<string.h>` library. You must use a loop and the null terminator.
    - _Hint:_ The loop condition can be `while (*src != '\0')`. Don't forget to add the null terminator to `dest` at the end!

---

## 6. Dynamic Memory Allocation (manual heap management)

In C#, the Common Language Runtime (CLR) provides a **Garbage Collector (GC)** that automatically reclaims memory that is no longer in use. You allocate objects with `new`, and the GC handles the rest. In C, there is no such luxury. Memory management is your explicit responsibility. You must manually request memory from the operating system and, crucially, you must manually return it when you are finished. This is the art and science of **dynamic memory allocation** and it is a defining feature of C programming.

## 6.1. The Heap vs. The Stack

To understand dynamic memory allocation, you must first understand the two primary regions of memory available to your program: the **stack** and the **heap**.

- **The Stack:** This is a LIFO (Last-In, First-Out) memory region used for local variables, function parameters, and return addresses. When you call a function, a new **stack frame** is pushed onto the stack. When the function returns, its stack frame is popped off, and all local variables within it are automatically destroyed. Stack allocation is extremely fast but is limited in size. Variables declared inside a function without a special keyword (e.g., `int x = 10;`) are allocated on the stack.

- **The Heap:** This is a large, unstructured pool of memory that the operating system manages. You can request chunks of memory from the heap at any time during your program's execution. Unlike the stack, memory on the heap is not automatically freed when a function returns; it persists until you explicitly release it or the program terminates. This flexibility comes at a cost: heap allocation and deallocation are slower than stack operations. C# objects (e.g., `new MyClass()`) are allocated on the heap.

The key takeaway is this: for any data whose size is not known at compile-time or whose lifetime needs to extend beyond the scope of a single function call, you must use the heap.

## 6.2. `malloc`, `calloc`, `realloc`, and `free`

The C Standard Library provides four fundamental functions in `<stdlib.h>` for managing heap memory.

#### `malloc` (Memory Allocate)

This is the most common function for dynamic memory allocation. It requests a specified number of bytes from the heap.

- **Signature:** `void* malloc(size_t size);`
- **Purpose:** Allocates `size` bytes of uninitialized memory.
- **Returns:** A `void*` pointer to the start of the allocated block on success, or `NULL` on failure.

Since `malloc` returns a `void*` (a generic pointer), you must **cast** it to the specific pointer type you need.

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    // Allocate a single integer on the heap
    int* ptr = (int*)malloc(sizeof(int));

    if (ptr == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }

    *ptr = 42;
    printf("Value on the heap: %d\n", *ptr);

    // Free the allocated memory
    free(ptr);
    ptr = NULL; // Best practice: set pointer to NULL after freeing

    return 0;
}
```

#### `calloc` (Contiguous Allocate)

`calloc` is similar to `malloc` but with two key differences: it takes two arguments and initializes the allocated memory to zero.

- **Signature:** `void* calloc(size_t num, size_t size);`
- **Purpose:** Allocates memory for an array of `num` elements, each of size `size`, and initializes all bits to `0`.
- **Returns:** A `void*` pointer to the start of the allocated block, or `NULL` on failure.

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    // Allocate space for 5 integers and zero-initialize them
    int* numbers = (int*)calloc(5, sizeof(int));

    if (numbers == NULL) {
        printf("Memory allocation failed!\n");
        return 1;
    }

    // Since it's zero-initialized, this will print 0
    printf("First element: %d\n", numbers[0]);

    // Now you can use the memory as a normal array
    numbers[0] = 10;
    numbers[1] = 20;

    free(numbers);

    return 0;
}
```

#### `realloc` (Re-allocate)

`realloc` is used to resize a previously allocated memory block.

- **Signature:** `void* realloc(void* ptr, size_t new_size);`
- **Purpose:** Resizes the memory block pointed to by `ptr` to `new_size` bytes. The existing data is preserved.
- **Returns:** A `void*` pointer to the new memory block. This may be the same as `ptr` or a new, different address. Returns `NULL` on failure, in which case the original block is unchanged.

**Crucially, you must use the pointer returned by `realloc`**, as it may have moved the memory block to a new location.

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int* numbers = (int*)malloc(2 * sizeof(int));
    if (!numbers) return 1;
    numbers[0] = 10;
    numbers[1] = 20;

    printf("Original address: %p\n", numbers);

    // Resize the array to hold 4 integers
    int* new_numbers = (int*)realloc(numbers, 4 * sizeof(int));
    if (!new_numbers) {
        // If realloc fails, we must free the original block
        free(numbers);
        return 1;
    }

    numbers = new_numbers; // The pointer is updated
    printf("New address:      %p\n", numbers);
    numbers[2] = 30;
    numbers[3] = 40;

    // The original data is still there
    for(int i = 0; i < 4; i++) {
        printf("numbers[%d] = %d\n", i, numbers[i]);
    }

    free(numbers);
    return 0;
}
```

#### `free`

`free` is the counterpart to `malloc`, `calloc`, and `realloc`. It returns a block of memory to the heap. **Every call to `malloc`, `calloc`, or `realloc` must eventually be matched by a call to `free`**.

- **Signature:** `void free(void* ptr);`
- **Purpose:** Deallocates the memory block at the address `ptr`.
- **Note:** Passing `NULL` to `free` is explicitly safe and does nothing.

## 6.3. Manual Memory Management vs. C# Garbage Collection (GC)

This is a direct comparison of the two memory paradigms.

| Feature            | C (Manual)                                                                | C# (Garbage Collection)                                                              |
| ------------------ | ------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| **Responsibility** | Developer must manually allocate and free memory.                         | GC automatically reclaims unreachable memory.                                        |
| **Control**        | Absolute control over when memory is allocated/freed.                     | No direct control; depends on GC's algorithm.                                        |
| **Performance**    | Predictable, often faster due to no background overhead.                  | Small, unpredictable pauses during garbage collection.                               |
| **Common Errors**  | **Memory leaks**, dangling pointers, double-free bugs.                    | No memory leaks (usually), but can have performance issues with large object graphs. |
| **Flexibility**    | Can be used for constrained environments (embedded systems, kernel code). | Relies on the CLR and is less suitable for low-level systems programming.            |

The most common mistakes in C are memory leaks and dangling pointers. A **memory leak** occurs when you allocate memory with `malloc` but never call `free` to return it to the system. This leads to a gradual consumption of available memory. A **dangling pointer** is a pointer that still holds the address of a memory block that has already been freed. Using a dangling pointer (`*ptr`) is a severe error known as **use-after-free**, which can corrupt data or lead to a segmentation fault.

## 6.4. Best Practices: Checking for `NULL`, Avoiding Memory Leaks

Safe and robust dynamic memory management requires discipline. Here are the golden rules:

1.  **Always check the return value of `malloc` and `realloc`.** If memory allocation fails (e.g., due to insufficient memory), `malloc` returns `NULL`. Your program should handle this gracefully rather than crashing.
2.  **Every `malloc` must have a corresponding `free`.** Make a habit of writing the `free` call immediately after your `malloc` call, then fill in the code in between. This helps prevent forgetting to free the memory.
3.  **Do not `free` a pointer twice.** This is a **double-free error** and leads to undefined behavior. A good practice is to set the pointer to `NULL` after you free it, as `free(NULL)` is a safe no-op.
4.  **Do not use a pointer after it has been freed.** The memory it points to may have been reallocated or its contents may have changed. A dangling pointer is a bug waiting to happen.
5.  **Use `sizeof` to calculate allocation sizes.** This ensures your code is portable and correct, as type sizes can vary. Use `malloc(num_elements * sizeof(Type))`.

## Key Takeaways

- **Stack vs. Heap:** Stack is for local, automatic variables. Heap is for dynamically allocated memory with a lifetime you control.
- **`malloc` and `free` are a pair.** Use `malloc` to request memory and `free` to return it.
- **`calloc` zero-initializes memory**, which is safer than `malloc`'s uninitialized memory.
- **`realloc` is for resizing**, but be careful: it may return a new address, so you must always use the new pointer.
- **C's manual memory management requires diligence.** Failure to manage memory correctly can lead to serious bugs like memory leaks, dangling pointers, and double-free errors.

### Exercises

1.  **Vector Allocation:** Write a program that asks the user for a number, allocates an array of `int`s of that size on the heap using `malloc`, and then reads that many numbers from the user and stores them in the array. Finally, print the numbers and `free` the memory.

    - _Hint:_ The allocation will look like `int* arr = (int*)malloc(size_of_array * sizeof(int));`. Don't forget to check if `arr` is `NULL`!

2.  **String Duplication:** Write a function `char* my_strdup(const char* src)` that takes a source string and returns a new, dynamically allocated copy of the string on the heap. The returned string should be null-terminated. Your `main` function should test this by creating a string literal, passing it to `my_strdup`, and then printing both the original and the new string. Remember to `free` the duplicated string.

    - _Hint:_ You'll need `strlen` to determine the size of the new allocation and `strcpy` (or `strncpy`) to copy the data.

3.  **Interactive Resizing:** Write a program that starts with a dynamically allocated array of 2 integers. In a loop, prompt the user if they want to add another number. If they do, use `realloc` to resize the array to accommodate the new number. Keep track of the number of elements and the current size. Once the user is done, print all the numbers and then `free` the final array.
    - _Hint:_ This is a classic example of growing a dynamic array. You'll need to use a variable to keep track of the current element count.

---

## Where to Go Next

- **[Part I](./part1.md): C Fundamentals:** Setting up your environment, writing your first C program, and understanding the basic syntax and semantics of C.
- **[Part III](./part3.md): Advanced Data and Operations:** Exploring structures, unions, enums, bitwise operations, and advanced type system features.
- **[Part IV](./part4.md): Practical Tooling and Resources:** Learning how to debug C programs, use common tools, and find further resources for continued learning.
