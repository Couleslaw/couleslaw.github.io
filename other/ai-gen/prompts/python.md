---
layout: default
title: Prompt for Python Under the Hood | Jakub Smolik
---

[..](../python)

Act as an expert in Python’s internal architecture and execution model. You are the world’s foremost authority on how Python works “under the hood,” with decades of experience both implementing interpreters and teaching advanced developers.

Your task is to write, chapter by chapter, a guide for deeply understanding python under the hood. When you will be working on a chapter, follow the exact table of contents and cover every bullet in exhaustive detail, providing clear explanations, diagrams (described in text), and practical examples or best‑practice recommendations where appropriate. DONT WRITE ANYTHING JUST YET.

- Audience: Developers who already write non‑trivial Python code but lack a deep mental model of interpreter internals.

- Purpose: Empower them to write more predictable, performant, and maintainable Python by understanding what happens behind the scenes.

- Tone: Authoritative yet approachable, balancing technical depth with clarity.

**Response Format:**

- OUTPUT ALL GUIDE TEXT IN RAW MARKDOWN, enclosing it in `markdown [text]`. Use headings exactly matching the table of contents.
- When writing multiline python code snippets, use the following sytax with only 2 ticks `
`startpython
  [code]
  ``endpython
- Under each bullet, write 2–4 paragraphs of detailed narrative, with occasional python code examples when appropriate
- Where helpful, describe diagrams in words (e.g. “Imagine a layered stack: Source → Parser → Bytecode → PVM”).
- Conclude the chapter with a “Key Takeaways” bullet list.

**Thought Process Instructions:**

1. Before writing the actual guide content, list out your high‑level approach and outline of reasoning in bullet points.

2. Use chain‑of‑thought style: think step by step, compare alternatives (e.g., “When covering the GIL, weigh pros and cons of threads vs processes”), and simulate an internal expert debate if necessary.

**Advanced Prompt Techniques to Use Internally:**

- **Chain of Thought:** Explicitly break down complex explanations

- **Debate Simulation:** Where trade‑offs exist, model both sides before concluding

- **Self Reflection:** Pause at each section to check for completeness

- **Self Consistency:** Reconcile any contradictions before moving on

**Table of Contents**

# Python Under the Hood

## Introduction

- Purpose of this guide
- Target audience: Intermediate to advanced Python developers
- What you’ll gain: Mental model of Python's execution model and internal workings, best practices

---

## 1. The Python Landscape

- 1.1. A Brief History: From Python 2 to Python 3 and beyond
- 1.2. CPython vs PyPy vs Jython vs MicroPython
- 1.3. Python Distributions (Anaconda, standard Python.org, system packages)
- 1.4. The Python Standard Library and its Philosophy

---

## 2. Python's Execution Model

- 2.1. Is Python Interpreted or Compiled?
- 2.2. Understanding Python Bytecode (`.pyc` files)
- 2.3. The Python Virtual Machine (PVM)
- 2.4. Python’s Import System: Module resolution, import hooks, `sys.path`, `importlib`

---

## 3. Variables, Scope, and Namespaces

- 3.1. Name Binding: Names vs objects
- 3.2. Variable Lifetime and Identity
- 3.3. The LEGB Rule: Local, Enclosing, Global, Built‑in
- 3.4. Scope Introspection: `globals()`, `locals()`, `nonlocal`, `del`
- 3.5. Namespaces in modules, functions, and classes

---

## 4. Functions and Callables

- 4.1. First‑Class Functions and Closures
- 4.2. The Function Object: `__code__`, `__defaults__`, `__annotations__`
- 4.3. Argument Handling: `*args`, `**kwargs`, default values
- 4.4. Lambdas, Partial Functions, and Higher‑Order Functions
- 4.5. Decorators: Functional patterns and metadata preservation
- 4.6. Common Gotchas and How to Avoid Them

---

## 5. Classes, Objects, and Object‑Oriented Internals

- 5.1. Classes as Objects: `type`, `__class__`, and metaclasses
- 5.2. Instance vs Class Attributes
- 5.3. Method Resolution Order (MRO), `super()`, and C3 linearization
- 5.4. Dunder Methods and Data Model (`__init__`, `__new__`, `__getattr__`, etc.)
- 5.5. Dynamic Class Creation and Custom Metaclasses

---

## 6. Abstract Base Classes, Protocols, and Structural Typing

- 6.1. Abstract Base Classes with `abc.ABC`
- 6.2. `@abstractmethod` and Virtual Subclassing
- 6.3. Protocols and Structural Subtyping with `typing.Protocol`
- 6.4. Must Know Python Protocols
- 6.5. Choosing between ABCs and Protocols
- 6.6. Runtime type checks vs static interfaces

---

## 7. Type Annotations: History, Tools, and Best Practices

- 7.1. History of Type Annotations in Python
- 7.2. The Basics (built‑in annotations)
- 7.3. Type Inference and Type Comments (legacy and modern syntax)
- 7.4. Static Checkers: `mypy`, `pyright`, `pytype`, `pylance`
- 7.5. Gradual Typing and Best Practices for Large Codebases
- 7.6. Runtime Type Enforcement: `typeguard`, `beartype`, `pydantic`

---

## 8. Advanced Annotation Techniques: A State‑of‑the‑Art Guide

- 8.1. Annotating Every Built‑in and Standard‑Library API
- 8.2. Deep Annotation of Functions and Callables (`Callable`, `ParamSpec`, `Concatenate`)
- 8.3. Class and Data Structure Annotations: `TypedDict`, `NamedTuple`, `dataclass`
- 8.4. Generics and Parametrized Types (`TypeVar`, `Generic`, `PEP646 TypeVarTuple`)
- 8.5. Protocols and Structural Subtyping Revisited: Advanced Patterns
- 8.6. Asynchronous and Generator Annotations (`Awaitable`, `AsyncIterator`, `Generator`)
- 8.7. Best Practices for Large‑Scale Annotation: Project Layout, Stubs, and Incremental Adoption
- 8.8. Automation and Tooling: `pyannotate`, stubgen, and CI Integration

---

## 9. Runtime Memory Management Fundamentals

- 9.1. Everything is an Object: `PyObject` layout in CPython
- 9.2. Reference Counting and the Generational Garbage Collector
- 9.3. Object Identity and `id()`
- 9.4. Weak References and the `weakref` module
- 9.5. Tracking and Inspecting Memory Usage: `gc`, `tracemalloc`
- 9.6. Exception Handling and Stack Frames

---

## 10. Memory Allocator Internals & GC Tuning

- 10.1. The CPython Memory Allocator: obmalloc and Arenas
- 10.2. Small‑object Optimization and Free Lists
- 10.3. String Interning and Shared Objects
- 10.4. GC Tunables: Thresholds, Generations, and Collection Frequency
- 10.5. Optimizing Long‑Running Processes: Profiling and Tuning Strategies
- 10.6. Advanced `gc` Module Hooks and Callbacks

---

## 11. Concurrency, Parallelism, and Asynchrony

- 11.1. The Global Interpreter Lock (GIL)
- 11.2. Threads vs Processes: `threading` and `multiprocessing`
- 11.3. Futures, Executors, and Task Parallelism
- 11.4. Asynchronous Programming: `async`, `await`, and `asyncio`
- 11.5. Event Loops and Concurrency Control Patterns
- 11.6. Emerging Models: Subinterpreters and GIL‑Free Proposals

---

## 12. Debugging & Introspection Internals

- 12.1. The `inspect` Module: Source, Signatures, and Live Objects
- 12.2. Frame Introspection: Accessing and Modifying Stack Frames
- 12.3. Trace and Profile Hooks: `sys.settrace()`, `sys.setprofile()`
- 12.4. C‑Level Debugging: GDB, PyDBG, and CPython’s Debug Build
- 12.5. Runtime Hooks and Tracing APIs: `faulthandler`, `pydevd`
- 12.6. Building Custom Debuggers and Instrumentation Tools

---

## 13. Packaging and Dependency Management

- 13.1. What is a Python Package?
- 13.2. `pip`, `setuptools`, and `pyproject.toml`
- 13.3. Virtual Environments and `venv`
- 13.4. Dependency Resolution and Lockfiles (pip‑tools, poetry)
- 13.5. Wheels and Source Distributions
- 13.6. Quick Poetry Guide

---

## 14. Performance and Optimization

- 14.1. Finding Bottlenecks (cProfile, line_profiler)
- 14.2. Code Optimization Patterns in Python
- 14.3. Native Compilation with Cython, Numba, and PyPy
- 14.4. Useful Performance Decorators

---

## 15. Python in Production

- 15.1. Deployment: Source, Wheels, Frozen Binaries
- 15.2. Packaging with PyInstaller, Nuitka, and `shiv`
- 15.3. Docker and Python: Images, Dependency Isolation, and Reproducibility
- 15.4. Logging, Monitoring, and Observability
- 15.5. Environment Reproducibility in DevOps and CI/CD

---

## 16. Jupyter Notebooks and Interactive Computing

- 16.1. What Is a Jupyter Notebook?
- 16.2. Architecture: Notebook Server, Kernels, and `.ipynb` Files
- 16.3. Rich Output: Inline Plots, LaTeX, Images, and HTML
- 16.4. Useful Extensions: `nbextensions`, JupyterLab, Interactive Widgets
- 16.5. Data Science Workflows: Pandas, Matplotlib, Seaborn, Altair
- 16.6. Using Notebooks for Teaching, Demos, and Prototypes
- 16.7. Version Control Considerations and Best Practices
- 16.8. Converting Notebooks: `nbconvert`, `papermill`, `voila`, and Markdown Export

---

## 17. Summary and Mental Model

- Python layers: Source → Bytecode → PVM → OS
- Visual diagram: Python execution model
- Practical checklist for modern Python development

---

## Tools Every Python Developer Should Know

- IDEs: PyCharm, VS Code
- Debuggers: `pdb`, `ipdb`, VS Code's integrated tools
- Linters and Formatters: `flake8`, `black`, `isort`
- Testing: `pytest`, `unittest`, `tox`
- Static Type Checking: `mypy`, `pyright`
- Build Tools: `hatch`, `poetry`, `setuptools`

---

## Libraries That Matter – Quick Overview

### Standard Library Essentials

- `collections`, `itertools`, `functools`, `datetime`, `pathlib`, `concurrent.futures`
- `json`, `csv`, `re`, `os`, `shutil`, `subprocess`

### Data and Computation

- `numpy`: Multidimensional arrays and linear algebra
- `pandas`: Tabular data manipulation
- `scipy`: Scientific computing
- `math` and `statistics`: Numerical analysis

### Web and APIs

- `requests`: HTTP client
- `httpx`: Async‑capable alternative to requests
- `fastapi` and `flask`: Web frameworks
- `pydantic`: Data validation and parsing

### Files, Parsing, and I/O

- `openpyxl`, `pytables`, `h5py` for structured data files
- `lxml`, `BeautifulSoup`, `xml.etree.ElementTree` for parsing
- `PyYAML`, `toml`, `configparser` for config files

### Testing and Debugging

- `pytest`, `unittest`, `hypothesis`
- `pdb`, `ipdb`, `traceback`, `logging`

### CLI and Automation

- `argparse`, `click`, `typer`
- `rich` and `textual` for rich terminal UIs

### Machine Learning and Visualization

- `scikit‑learn`, `xgboost`, `tensorflow`, `PyTorch`
- `matplotlib`, `seaborn`, `plotly`, `altair`

### Developer Utilities

- `black`, `isort`, `flake8`, `mypy`
- `invoke`, `doit`, `watchdog` for automation
- `dotenv`, `loguru`, `attrs`, `dataclasses`, `tqdm`

### When and How to Choose the Right Library

- Maturity, documentation, community adoption
- License, performance, compatibility

---

## Appendix

- Glossary of terms: PEP, GIL, C Extension, Wheel, etc.
- Comparison of interpreters and runtimes
- Recommended reading and links to official docs
