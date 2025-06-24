---
layout: page
title: Python Under the Hood | Jakub Smolik
---

This article was [generated](./prompt.md) using Gemini and ChatGTP (free versions).

[view as PDF document instead](python.pdf)

# Python Under the Hood

#### Purpose

This guide serves as a deep dive into the internal workings of Python, specifically the CPython reference interpreter. Its purpose is to demystify what happens when your Python code runs. We will move beyond the syntax and semantics you already know to explore the architecture and design decisions that make Python the dynamic, flexible, and powerful language it is. By understanding the "why" behind the "how," you can write code that is not only correct but also more efficient and idiomatic.

#### Target Audience

This material is for you if you're comfortable writing Python applications, understand its object-oriented features, and have experience with its standard library. You might be a web developer, data scientist, or systems engineer who wants to move from being a proficient user of the language to an expert who can reason about performance, diagnose complex bugs, and make informed architectural choices based on a solid understanding of the runtime environment.

#### Structure

The guide is structured into six comprehensive parts, each building upon the last to provide a complete understanding of Python's internals:

- **[Part I](./part1.md): The Python Landscape and Execution Model:** Delving into Python's history, implementations, and the execution model that transforms your code into running programs.
- **[Part II](./part2.md): Core Language Concepts and Internals:** Exploring variables, scope, namespaces, the import system, functions, and classes in depth.
- **[Part III](./part3.md): Advanced Type System and Modern Design:** Mastering abstract base classes, protocols, type annotations, and advanced annotation techniques that enhance code reliability and maintainability.
- **[Part IV](./part4.md): Memory Management and Object Layout:** Understanding Python's memory model, object layout, and the garbage collection mechanisms that keep your applications running smoothly.
- **[Part V](./part5.md): Performance, Concurrency, and Debugging:** Examining concurrency models, performance optimization techniques, and debugging strategies that help you write efficient and robust code.
- **[Part VI](./part6.md): Building, Deploying, and The Developer Ecosystem:** Covering packaging, dependency management, production deployment, and the essential tools and libraries that every Python developer should know.
- **[Appendix](./appendix):** A collection of key takeaways, practical checklists and additional resources to solidify your understanding.

#### Learning Outcomes

Upon completing this guide, you will possess a robust mental model of the Python execution pipeline, from source code to machine interaction. You will understand memory management, the intricacies of the Global Interpreter Lock (GIL), the object model, and the type system. This knowledge will empower you to write more performant code, debug with greater precision, and leverage advanced language features with confidence. You'll learn best practices rooted not in convention alone, but in the fundamental truths of how Python operates.

## Table of Contents

### [Part I: The Python Landscape and Execution Model](./part1.md)

#### [1. The Python Landscape](./part1.md#1-the-python-landscape-1)

- 1.1. A Brief History of Python
- 1.2. Python Implementations
- 1.3. Python Distributions
- 1.4. The Standard Library Philosophy

#### [2. Python's Execution Model](./part1.md#2-pythons-execution-model-1)

- 2.1. Is Python Interpreted or Compiled?
- 2.2. Understanding Python Bytecode
- 2.3. The Python Virtual Machine
- 2.4. Python's Object Model
- 2.5. Memory Management

### [Part II: Core Language Concepts and Internals](./part2.md)

#### [3. Variables, Scope, and Namespaces](./part2.md#3-variables-scope-and-namespaces-1)

- 3.1. Name Binding: Names vs. Objects
- 3.2. Variable Lifetime and Identity
- 3.3. The LEGB Name Resolution Rule
- 3.4. Runtime Scope Introspection
- 3.5. Namespaces in Modules, Functions, and Classes

#### [4. Python's Import System](./part2.md#4-pythons-import-system-1)

- 4.1. Module Resolution
- 4.2. Specific Object Imports
- 4.3. Absolute vs. Relative Imports and Packages
- 4.4. Circular Imports and Reloading
- 4.5. Advanced Import Mechanisms

#### [5. Functions and Callables](./part2.md#5-functions-and-callables-1)

- 5.1. Functions & Closures
- 5.2. Inside The Function Object
- 5.3. Argument Handling: `*args` and `**kwargs`
- 5.4. Lambdas & Higher‑Order Functions
- 5.5. Function Decorators

#### [6. Classes and Data Structures](./part2.md#6-classes-and-data-structures-1)

- 6.1. Classes as Objects
- 6.2. Instance vs. Class Attributes
- 6.3. Method Resolution Order and `super()`
- 6.4. Dunder Methods
- 6.5. Private Attributes
- 6.6. Metaclasses
- 6.7. Class Decorators
- 6.8. Slotted Classes
- 6.9. Dataclasses
- 6.10. Essential Decorators

### [Part III: Advanced Type System and Modern Design](./part3.md)

#### [7. Abstract Base Classes, Protocols, and Structural Typing](./part3.md#7-abstract-base-classes-protocols-and-structural-typing-1)

- 7.1. Abstract Base Classes
- 7.2. Virtual Subclassing
- 7.3. Protocols (Python Interfaces)
- 7.4. Must Know Python Protocols
- 7.5. Runtime Checks vs. Static Analysis

#### [8. Type Annotations: History, Tools, and Best Practices](./part3.md#8-type-annotations-history-tools-and-best-practices-1)

- 8.1. Type Annotations History
- 8.2. The Basics of Type Annotation
- 8.3. Type Comments (Legacy)
- 8.4. Static Type Checkers
- 8.5. Gradual Typing in Large Codebases
- 8.6. Runtime Type Enforcement

#### [9. Advanced Annotation Techniques](./part3.md#9-advanced-annotation-techniques-1)

- 9.1. Annotating Built‑ins
- 9.2. Annotating Callables
- 9.3. Annotating User Defined Classes
- 9.4. Annotating Data Structures
- 9.5. Annotating Generic Classes
- 9.6. Large‑Scale Adoption
- 9.7. Automation & CI Integration

### [Part IV: Memory Management and Object Layout](./part4.md)

#### [10. Deep Dive Into Object Memory Layout](./part4.md#10-deep-dive-into-object-memory-layout-1)

- 10.1. The Great Unification: PyObject Layout
- 10.2. Memory Layout of User Defined Classes
- 10.3. Memory Layout of Slotted Classes
- 10.4. Memory Layout of Core Built-ins

#### [11. Runtime Memory Management & Garbage Collection](./part4.md#11-runtime-memory-management--garbage-collection-1)

- 11.1. PyObject Layout (Revision)
- 11.2. Reference Counting & The Garbage Collector
- 11.3. Object Identity and Object Reuse
- 11.4. Weak References
- 11.5. Memory Usage Tracking
- 11.6. Stack Frames & Exceptions

#### [12. Memory Allocator Internals & GC Tuning](./part4.md#12-memory-allocator-internals--gc-tuning-1)

- 12.1. Memory Allocation: `obmalloc` and Arenas
- 12.2. Small-object Optimizations: Free Lists
- 12.3. String Interning
- 12.4. GC Tunables and Thresholds
- 12.5. Optimizing Long Running Processes
- 12.6. GC Hooks and Callbacks

### [Part V: Performance, Concurrency, and Debugging](./part5.md)

#### [13. Concurrency, Parallelism, and Asynchrony](./part5.md#13-concurrency-parallelism-and-asynchrony-1)

- 13.1. The Global Interpreter Lock (GIL)
- 13.2. Multithreading vs. Multiprocessing
- 13.3. Futures & Task Executors
- 13.4. Asynchronous Programming: async/await
- 13.5. The Event Loop of `asyncio`
- 13.6. Emerging GIL-free Models

#### [14. Performance and Optimization](./part5.md#14-performance-and-optimization-1)

- 14.1. Finding Bottlenecks
- 14.2. Numerics with NumPy Arrays
- 14.3. Pythonic Optimizations
- 14.4. Native Compilation
- 14.5. Useful Performance Decorators

#### [15. Logging, Debugging and Introspection](./part5.md#15-logging-debugging-and-introspection-1)

- 15.1. Logging Done Properly: `logging`
- 15.2. Runtime Object Introspection: `inspect`
- 15.3. Runtime Stack Frame Introspection
- 15.3. Interpreter Profiling Hooks
- 15.4. C‑Level Debugging
- 15.4. Diagnosing Unexpected Crashes: `faulthandler`
- 15.5. Building Custom Debuggers

### [Part VI: Building, Deploying, and The Developer Ecosystem](./part6.md)

#### [16. Packaging and Dependency Management](./part6.md#16-packaging-and-dependency-management-1)

- 16.1. What is a Python Package?
- 16.2. `pip`, `setuptools` and `pyproject.toml`
- 16.3. Virtual Environments
- 16.4. Dependency Resolution and Lockfiles
- 16.5. Wheels and Source Distributions
- 16.6. Poetry Quickstart

#### [17. Python in Production](./part6.md#17-python-in-production-1)

- 17.1. Code Testing Fundamentals and Best Practices
- 17.2. Deployment: Source, Wheels and Frozen Binaries
- 17.3. Packaging Tools: PyInstaller, Nuitka and Shiv
- 17.4. Docker, Containerization and Reproducibility
- 17.5. Logging, Monitoring and Observability
- 17.6. CI/CD Environment Reproducibility

#### [18. Jupyter Notebooks and Interactive Computing](./part6.md#18-jupyter-notebooks-and-interactive-computing-1)

- 18.1. What is a Jupyter Notebook?
- 18.2. Architecture: Notebook Server, Kernels and Client
- 18.3. Rich Media Display with MIME
- 18.4. Popular Extensions and Plugins
- 18.5. Data Analysis Workflows
- 18.6. Parallelism in Jupyter Notebooks
- 18.7. Jupyter Notebook Usecases
- 18.8. Jupyter Notebooks & Version Control
- 18.9. Converting Notebooks

#### [19. Tools Every Python Developer Should Know](./part6.md#19-tools-every-python-developer-should-know-1)

- 19.1. IDEs: PyCharm & VSCode
- 19.2. Debuggers
- 19.3. Linters and Formatters
- 19.4. Testing Frameworks
- 19.5. Static Type Checkers
- 19.6. Build Tools

#### [20. Libraries That Matter – Quick Overview](./part6.md#20-libraries-that-matter--quick-overview-1)

- 20.1. Standard Library Essentials
- 20.2. Data and Computation
- 20.3. Web and APIs
- 20.4. File Parsing and I/O
- 20.5. Threading and Concurrency
- 20.6. Testing and Debugging
- 20.7. CLI and Automation
- 20.8. Machine Learning and Visualization
- 20.9. Developer Utilities
- 20.10. How to Choose the Right Library?

### [Appendix](./appendix.md)

- A.1. Glossary of Terms
- A.2. Summary of Python Layers
- A.3. Python Development Checklist
- A.4. Python Runtimes Comparison
- A.5. Further Reading
