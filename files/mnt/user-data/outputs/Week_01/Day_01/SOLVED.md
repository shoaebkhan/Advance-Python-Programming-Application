# Week 1 — Day 1: Introduction to Python — History, Interpreters vs. Compilers

**PCAP-31-03 Curriculum Domain Mapping:** Module 1 — Basics of the Python Language
*(1.1 Introduction to Python: fundamentals of computer programming, interpreting vs. compiling, Python keywords, `print()`, and the interactive/script execution model)*

---

## Topic Overview

Python is a high-level, general-purpose, **interpreted** programming language created by **Guido van Rossum**, first released in 1991 and named after the British comedy troupe *Monty Python's Flying Circus*. Unlike a **compiled language** such as C — where source code is translated in one pass into a standalone machine-code executable before it ever runs — Python source code (`.py` files) is first translated by the reference implementation, **CPython**, into an intermediate low-level representation called **bytecode**. This bytecode is then executed line-by-line by the **Python Virtual Machine (PVM)**. This hybrid model (compile-to-bytecode, then interpret) is why Python is commonly described as "interpreted," even though a genuine compilation step does occur internally.

Understanding this distinction matters for the PCAP exam and for real debugging: because Python is interpreted, syntax errors are typically only discovered when the interpreter reaches that specific line, not before the program starts (unlike a compiler, which rejects the entire program upfront on any syntax violation). Python also offers two execution modes: **interactive mode** (the REPL — Read-Eval-Print Loop — launched by typing `python` or `python3` with no arguments) for quick, line-by-line experimentation, and **script mode** (`python filename.py`) for running a complete program from a file. CPython caches compiled bytecode for imported modules in a `__pycache__` directory as `.pyc` files, so repeated imports skip re-compilation and load faster.

For the PCAP-31-03 exam, you should be able to: distinguish interpreters from compilers conceptually; explain why Python programs can begin executing before the entire file is verified as free of errors; identify what `print()` does in script mode versus how the interactive shell echoes expression results automatically; and recognize basic facts about Python's design philosophy (readability, dynamic typing, garbage collection) as motivations behind language choices tested throughout the exam.

---

## Example 1 (Normal)

### Problem Statement
Write a Python script that prints a welcome message, then prints the currently running Python version and its implementation name (e.g., CPython), demonstrating basic script-mode execution.

### Python Solution
```python
"""
hello_python.py
Demonstrates basic script-mode execution and version introspection.
"""
import sys
import platform


def display_welcome_message() -> None:
    """Print a simple welcome banner to the terminal."""
    print("Welcome to Advanced Python Programming!")


def display_interpreter_info() -> None:
    """Print the running Python version and interpreter implementation."""
    version = sys.version.split()[0]
    implementation = platform.python_implementation()
    print(f"Running on {implementation} version {version}")


if __name__ == "__main__":
    display_welcome_message()
    display_interpreter_info()
```

### Expected Terminal Output
```
Welcome to Advanced Python Programming!
Running on CPython version 3.12.3
```
*(Exact version number will vary depending on the installed interpreter.)*

### Step-by-Step Code Explanation
1. `import sys` and `import platform` bring in two Standard Library modules used purely for introspection — no third-party packages needed.
2. `display_welcome_message()` isolates the greeting into its own function — a PCAP-relevant habit of keeping logic organized even in trivial scripts.
3. `sys.version` returns a long descriptive string; `.split()[0]` extracts just the numeric version (e.g., `"3.12.3"`).
4. `platform.python_implementation()` returns `"CPython"`, `"PyPy"`, `"Jython"`, or `"IronPython"` depending on which interpreter is executing the code — useful for confirming which implementation is running.
5. `if __name__ == "__main__":` ensures the function calls only run when the file is executed directly as a script (not when imported as a module elsewhere) — this guard is a foundational Python idiom tested conceptually on the PCAP exam.

---

## Example 2 (Normal-Intermediate)

### Problem Statement
Demonstrate the difference between **interactive mode** and **script mode** by writing a short script that shows what must be explicit in script mode (using `print()`) that the interactive shell does automatically.

### Python Solution
```python
"""
interactive_vs_script.py
Illustrates why script mode requires explicit print() calls.
"""

# In interactive mode, simply typing an expression like:
#     >>> 5 + 3
# automatically echoes the result: 8
# In script mode, the same line produces NO visible output at all,
# because script mode does not auto-print expression results.

result = 5 + 3          # Evaluated, but nothing is shown without print()
print(result)            # Must explicitly print to see output in script mode

name = "Python"
greeting = f"Hello, {name}!"
print(greeting)          # Explicit print required again
```

### Expected Terminal Output
```
8
Hello, Python!
```

### Step-by-Step Code Explanation
1. The comment block explains the conceptual difference the example is built to demonstrate: the interactive shell (REPL) automatically displays the result of any bare expression, while a script does not.
2. `result = 5 + 3` computes `8` and stores it in `result`, but running this line alone in a script produces no terminal output.
3. `print(result)` is required to make that value visible — this is a common early-exam trap question: "what happens if you remove `print()` from a script?" (Answer: nothing prints; the value is simply discarded after evaluation.)
4. The same pattern repeats with an f-string greeting, reinforcing that **every** piece of output in script mode must go through an explicit output function like `print()`.

---

## Example 3 (Intermediate)

### Problem Statement
Write a script that uses the `dis` (disassembler) module to reveal the actual **bytecode instructions** the CPython compiler generates for a simple function, demonstrating that "interpreted" Python still involves a real compilation step.

### Python Solution
```python
"""
bytecode_inspector.py
Uses the dis module to reveal CPython's compiled bytecode for a function.
"""
import dis


def add_two_numbers(a: int, b: int) -> int:
    """Return the sum of two integers."""
    return a + b


def main() -> None:
    """Disassemble add_two_numbers() and print its bytecode instructions."""
    print("Bytecode for add_two_numbers():")
    dis.dis(add_two_numbers)


if __name__ == "__main__":
    main()
```

### Expected Terminal Output
```
Bytecode for add_two_numbers():
  8           0 RESUME                   0

  9           2 LOAD_FAST                0 (a)
              4 LOAD_FAST                1 (b)
              6 BINARY_OP                0 (+)
             10 RETURN_VALUE
```
*(Exact opcode names/columns vary slightly by Python version — the presence of low-level opcodes like `LOAD_FAST` and `BINARY_OP` is the key takeaway, not the exact formatting.)*

### Step-by-Step Code Explanation
1. `import dis` brings in Python's built-in disassembler, part of the Standard Library, used to inspect compiled bytecode.
2. `add_two_numbers()` is a trivially simple function chosen deliberately so the resulting bytecode is short and readable.
3. `dis.dis(add_two_numbers)` compiles the function (if not already compiled) and prints its bytecode instruction sequence — each line is a single PVM (Python Virtual Machine) operation.
4. This output is direct proof that Python source is **not** interpreted character-by-character at runtime; it is first compiled into bytecode instructions, which the PVM then executes — the exact hybrid model described in the Topic Overview.
5. This example reinforces a nuanced PCAP concept: the label "interpreted language" describes the *end-user execution model*, not the absence of any compilation step internally.

---

## Example 4 (Complex/Exam-Level)

### Problem Statement
Write a script that programmatically demonstrates the `.pyc` bytecode caching behavior: create a small importable module, import it, and inspect the `__pycache__` directory to confirm a compiled cache file was generated.

### Python Solution
```python
"""
pycache_demo.py
Demonstrates .pyc bytecode caching by creating and importing a module,
then inspecting the resulting __pycache__ directory.
"""
import importlib
import os
import sys


def create_sample_module(directory: str) -> str:
    """
    Write a minimal importable module to disk.

    Args:
        directory (str): Target directory for the new module file.

    Returns:
        str: The path to the created module file.
    """
    module_path = os.path.join(directory, "sample_module.py")
    with open(module_path, "w", encoding="utf-8") as module_file:
        module_file.write('"""A trivial sample module for cache demonstration."""\n')
        module_file.write("MESSAGE = 'Loaded from sample_module'\n")
    return module_path


def import_and_inspect_cache(directory: str) -> None:
    """
    Import the sample module and list any generated __pycache__ contents.

    Args:
        directory (str): Directory containing the sample module.
    """
    sys.path.insert(0, directory)
    import sample_module  # noqa: F401  (imported for its side effect: cache creation)

    importlib.reload(sample_module)  # forces recompilation if needed
    print(f"Imported message: {sample_module.MESSAGE}")

    cache_dir = os.path.join(directory, "__pycache__")
    if os.path.isdir(cache_dir):
        cached_files = os.listdir(cache_dir)
        print(f"__pycache__ contents: {cached_files}")
    else:
        print("No __pycache__ directory was created.")


if __name__ == "__main__":
    working_dir = os.getcwd()
    create_sample_module(working_dir)
    import_and_inspect_cache(working_dir)
```

### Expected Terminal Output
```
Imported message: Loaded from sample_module
__pycache__ contents: ['sample_module.cpython-312.pyc']
```
*(The exact `.pyc` filename encodes the interpreter tag and version, e.g., `cpython-312`, and will vary by installed Python version.)*

### Step-by-Step Code Explanation
1. `create_sample_module()` writes a tiny `.py` file to disk at runtime — this stands in for "any importable module."
2. `sys.path.insert(0, directory)` ensures Python's import system can find the newly created module in the current working directory.
3. `import sample_module` triggers CPython's import machinery, which compiles the source to bytecode and — because this is a module import, not a top-level script — writes a `.pyc` cache file into an auto-created `__pycache__` folder.
4. `importlib.reload()` is used defensively to force re-evaluation in case the module was already cached from a prior run in the same session.
5. Listing `__pycache__`'s contents provides direct, observable proof of the caching behavior described conceptually in the Topic Overview: **top-level scripts are compiled every run, but imported modules are cached as `.pyc` files** to speed up subsequent imports — a subtle but exam-relevant distinction.

---

## Example 5 (Complex/Exam-Level)

### Problem Statement
Write a script that deliberately demonstrates Python's **run-until-error** execution behavior: a script with a syntax-valid but runtime-failing statement placed *after* several valid `print()` statements, showing that everything before the failure point still executes — a behavior that would differ in a fully ahead-of-time compiled language.

### Python Solution
```python
"""
run_until_error_demo.py
Demonstrates that Python executes statements sequentially and prints
everything up to the point of a runtime error, rather than refusing
to run the whole program upfront.
"""


def divide_numbers(numerator: int, denominator: int) -> float:
    """
    Divide numerator by denominator.

    Args:
        numerator (int): The value to divide.
        denominator (int): The value to divide by.

    Returns:
        float: The division result.

    Raises:
        ZeroDivisionError: If denominator is zero.
    """
    return numerator / denominator


def main() -> None:
    """Print several lines, then trigger a runtime error deliberately."""
    print("Step 1: Program started")
    print("Step 2: Performing a valid calculation")
    print(f"Step 2 result: {divide_numbers(10, 2)}")

    print("Step 3: Attempting an invalid calculation")
    print(f"Step 3 result: {divide_numbers(10, 0)}")  # Raises ZeroDivisionError here

    print("Step 4: This line will never execute")  # Unreachable


if __name__ == "__main__":
    main()
```

### Expected Terminal Output
```
Step 1: Program started
Step 2: Performing a valid calculation
Step 2 result: 5.0
Step 3: Attempting an invalid calculation
Traceback (most recent call last):
  File "run_until_error_demo.py", line 30, in <module>
    main()
  File "run_until_error_demo.py", line 24, in main
    print(f"Step 3 result: {divide_numbers(10, 0)}")
                             ^^^^^^^^^^^^^^^^^^^^^^
  File "run_until_error_demo.py", line 13, in divide_numbers
    return numerator / denominator
           ~~~~~~~~~~~^~~~~~~~~~~~
ZeroDivisionError: division by zero
```

### Step-by-Step Code Explanation
1. Steps 1–3's `print()` calls execute successfully and their output appears on the terminal **before** the program crashes — proof that CPython does not perform a full whole-program validation pass before running (as a strictly compiled language's linker/compiler would for certain classes of errors).
2. `divide_numbers(10, 0)` reaches the `return numerator / denominator` line, and CPython raises `ZeroDivisionError` at the moment that specific instruction executes — not earlier.
3. Because the exception is unhandled, the **traceback** is printed automatically, showing the full call stack from `main()` down to the exact failing line.
4. `Step 4`'s `print()` statement is never reached; execution halts entirely once an unhandled exception propagates to the top level.
5. This example is a classic PCAP-exam conceptual question in code form: *"What does this script output before it fails?"* — testing whether the student understands sequential, line-by-line interpretation rather than assuming all-or-nothing execution.
