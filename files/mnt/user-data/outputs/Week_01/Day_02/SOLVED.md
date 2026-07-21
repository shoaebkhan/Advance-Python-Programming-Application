# Week 1 — Day 2: Installation & IDEs — VS Code, PyCharm, Jupyter

**PCAP-31-03 Curriculum Domain Mapping:** Module 1 — Basics of the Python Language
*(1.1 Introduction to Python: running Python code — interactive vs. script mode across environments; comments; the `print()` function's full signature, including `sep` and `end`)*

---

## Topic Overview

While the PCAP-31-03 exam does not test IDE menus directly, it *does* test what happens once code leaves any given editor and reaches the interpreter — so today's focus is the practical bridge between development environments and correct execution. Three tools dominate professional and academic Python workflows: **VS Code** (a lightweight, extensible general-purpose editor with a first-class Python extension providing linting, debugging, and an integrated terminal), **PyCharm** (a full IDE purpose-built for Python, with deeper built-in refactoring, virtual environment management, and database/framework tooling), and **Jupyter Notebooks** (a cell-based, interactive execution environment popular in data science, where code runs in a persistent kernel rather than top-to-bottom as a single script). Each tool ultimately calls the same underlying Python interpreter — understanding *how* they invoke it (which interpreter, which working directory, which command-line arguments) prevents a large class of "it works on my machine" bugs.

A core, exam-relevant detail regardless of IDE is the **`print()` function's full signature**: `print(*objects, sep=' ', end='\n', file=sys.stdout, flush=False)`. Most beginners only ever use the positional arguments, but the PCAP exam frequently tests `sep` and `end` explicitly, since they control output formatting in ways that are easy to get wrong. Another cross-IDE fundamental is **comments**: Python supports single-line comments with `#` and, conventionally, multi-line documentation via triple-quoted strings (technically string literals, not true comments, but used as such for docstrings and block explanations). Finally, every IDE eventually needs to know **which Python interpreter** to run your code with — a concept that becomes critical tomorrow when we introduce virtual environments, and today's examples lay the groundwork by inspecting interpreter paths programmatically.

For the PCAP-31-03 exam, be ready to: predict exact output given specific `sep`/`end` values passed to `print()`; distinguish `#` comments from triple-quoted string literals; and understand that `sys.executable` identifies the specific interpreter binary running your code — a detail IDEs surface visually but which the exam expects you to reason about at the code level.

---

## Example 1 (Normal)

### Problem Statement
Write a script that demonstrates both single-line (`#`) and multi-line (triple-quoted string) commenting styles, and prints a properly formatted module description using both.

### Python Solution
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

"""
comment_styles.py

This module demonstrates the two primary Python commenting conventions:
1. Single-line comments using the '#' symbol.
2. Multi-line documentation using triple-quoted string literals,
   which Python treats as an unassigned expression (effectively ignored
   at runtime, but readable as documentation by humans and tools).
"""

# This is a single-line comment explaining the line below.
author_name = "Course Instructor"  # Inline comment: describes this specific variable

print(f"Module authored by: {author_name}")
```

### Expected Terminal Output
```
Module authored by: Course Instructor
```

### Step-by-Step Code Explanation
1. The first two lines are conventional: a **shebang line** (`#!/usr/bin/env python3`) telling Unix-like systems which interpreter to use when the file is executed directly, and an encoding declaration (largely legacy in Python 3, where UTF-8 is already the default source encoding, but still commonly seen).
2. The triple-quoted string immediately after is a **module-level docstring** — it is not assigned to a variable, so it produces no runtime output, but tools like `help()` and IDEs display it automatically.
3. `# This is a single-line comment...` is a true comment — everything after `#` on that line is ignored entirely by the interpreter.
4. The inline comment after `author_name = "Course Instructor"` demonstrates that comments can follow code on the same line, not just stand alone.
5. Only the `print()` call produces visible output — comments and docstrings are invisible at runtime regardless of which IDE ran the file.

---

## Example 2 (Normal-Intermediate)

### Problem Statement
Write a script that demonstrates the `print()` function's `sep` and `end` keyword arguments by formatting a list of values into a single comma-separated line without a trailing newline, followed immediately by a second `print()` call continuing on the same visual line.

### Python Solution
```python
"""
print_formatting.py
Demonstrates print()'s sep and end keyword arguments — a frequent
PCAP-31-03 exam topic.
"""

fruits = ["apple", "banana", "cherry"]

# Default behavior: sep=' ', end='\n'
print("Default:", fruits[0], fruits[1], fruits[2])

# Custom sep: join values with ", " instead of the default single space
print(fruits[0], fruits[1], fruits[2], sep=", ")

# Custom end: suppress the trailing newline so the next print() continues
# on the same terminal line
print("Loading", end="")
print("...", end="")
print("Done!")
```

### Expected Terminal Output
```
Default: apple banana cherry
apple, banana, cherry
Loading...Done!
```

### Step-by-Step Code Explanation
1. The first `print()` call uses default settings: multiple positional arguments are joined by a single space (`sep=' '`), and a newline is appended at the end (`end='\n'`) — this is the behavior most beginners assume is the *only* behavior.
2. The second call explicitly sets `sep=", "`, changing how the three fruit names are joined — note that `sep` only affects the separator *between* arguments, not before the first or after the last.
3. The third block calls `print()` three times with `end=""` on the first two calls, which **suppresses** the automatic newline, causing all three calls to render on a single visual terminal line (`Loading...Done!`).
4. The final `print("Done!")` call uses the default `end='\n'`, so a newline is added after it, cleanly ending the line.
5. This exact combination of `sep`/`end` reasoning — "what does this exact print() call output, character for character?" — is a classic PCAP-31-03 multiple-choice question format.

---

## Example 3 (Intermediate)

### Problem Statement
Write a script that accepts command-line arguments (as would be configured in a VS Code `launch.json` or a PyCharm Run Configuration) and prints them individually, demonstrating how IDEs pass arguments to the same underlying `sys.argv` mechanism regardless of which tool launched the script.

### Python Solution
```python
"""
argv_demo.py
Demonstrates reading command-line arguments via sys.argv — the same
mechanism IDEs use under the hood when you configure "script arguments"
in a Run Configuration.
"""
import sys


def display_arguments() -> None:
    """Print the script name and any additional command-line arguments."""
    script_name = sys.argv[0]
    additional_args = sys.argv[1:]

    print(f"Script executed: {script_name}")

    if additional_args:
        print(f"Received {len(additional_args)} argument(s):")
        for index, argument in enumerate(additional_args, start=1):
            print(f"  {index}. {argument}")
    else:
        print("No additional arguments were provided.")


if __name__ == "__main__":
    display_arguments()
```

### Expected Terminal Output
Running `python argv_demo.py alpha beta gamma` from a terminal (or an equivalent IDE Run Configuration with those three arguments configured):
```
Script executed: argv_demo.py
Received 3 argument(s):
  1. alpha
  2. beta
  3. gamma
```
Running with no arguments (`python argv_demo.py`):
```
Script executed: argv_demo.py
No additional arguments were provided.
```

### Step-by-Step Code Explanation
1. `sys.argv` is a list where index `0` is always the script's own filename (or path, depending on how it was invoked) — this is true whether launched from a terminal, VS Code, or PyCharm.
2. `sys.argv[1:]` slices off everything **except** index 0, giving just the arguments the user (or IDE configuration) supplied.
3. The `for index, argument in enumerate(additional_args, start=1):` loop numbers each argument starting at 1 for a human-readable listing.
4. The `if additional_args:` check relies on Python's truthiness rules — an empty list is falsy, so this branch correctly detects the "no arguments" case without needing `len(additional_args) == 0`.
5. This example is directly relevant to IDE workflows: VS Code's `launch.json` has an `"args"` array, and PyCharm's Run/Debug Configurations have a "Parameters" field — both ultimately populate this exact `sys.argv` list.

---

## Example 4 (Complex/Exam-Level)

### Problem Statement
Write an "environment inspector" script that reports the exact interpreter executable path currently running the code, its search path for imports, and the current working directory — information that explains many common IDE misconfiguration bugs (e.g., "wrong Python version selected," "module not found" despite being installed).

### Python Solution
```python
"""
environment_inspector.py
Reports interpreter path, import search path, and working directory —
diagnostic information relevant to IDE interpreter configuration issues.
"""
import os
import sys


def report_interpreter_path() -> None:
    """Print the absolute path of the currently running Python executable."""
    print(f"Interpreter executable: {sys.executable}")


def report_working_directory() -> None:
    """Print the current working directory the script is running from."""
    print(f"Current working directory: {os.getcwd()}")


def report_import_search_path(limit: int = 3) -> None:
    """
    Print the first few entries of the module import search path.

    Args:
        limit (int): Maximum number of sys.path entries to display.
    """
    print(f"First {limit} import search path entries:")
    for entry in sys.path[:limit]:
        print(f"  - {entry if entry else '(current directory)'}")


if __name__ == "__main__":
    report_interpreter_path()
    report_working_directory()
    report_import_search_path()
```

### Expected Terminal Output
```
Interpreter executable: /usr/bin/python3.12
Current working directory: /home/student/week1_projects
First 3 import search path entries:
  - (current directory)
  - /usr/lib/python3.12
  - /usr/lib/python3.12/lib-dynload
```
*(Exact paths depend entirely on the operating system, IDE, and installation method — the important pattern is the structure of the output, not these specific values.)*

### Step-by-Step Code Explanation
1. `sys.executable` gives the **absolute path to the exact interpreter binary** currently executing your code — this is the single most useful diagnostic when an IDE appears to be "using the wrong Python" (e.g., Python 2 vs. 3, or the wrong virtual environment, a concept formalized tomorrow).
2. `os.getcwd()` reports the working directory, which is often *not* the same folder your script file lives in — a frequent source of "file not found" errors when a script tries to open a relative path.
3. `sys.path` is the ordered list of directories Python searches when you write `import something`; an empty string entry (handled here with `entry if entry else '(current directory)'`) represents the script's own directory.
4. Slicing `sys.path[:limit]` keeps the output short and readable rather than dumping the full (often long) search path.
5. Every IDE — VS Code, PyCharm, Jupyter — ultimately configures these exact three things (interpreter binary, working directory, `sys.path`) differently under the hood; understanding them at the code level lets you diagnose environment problems independent of which specific IDE you're using.

---

## Example 5 (Complex/Exam-Level)

### Problem Statement
Write a script demonstrating the difference between a Jupyter-style persistent execution model and standard script-mode execution by simulating "cell state" persistence using function calls, and explain in code comments why this matters when moving code between a notebook and a `.py` file.

### Python Solution
```python
"""
notebook_vs_script_model.py
Simulates the conceptual difference between Jupyter's persistent-kernel
"cell" execution model and standard top-to-bottom script execution.
"""


def simulate_cell_1() -> int:
    """
    Simulates Jupyter 'Cell 1': defines a variable that would persist
    in the kernel's memory across all subsequent cell executions.
    """
    counter = 10
    print(f"[Cell 1] counter initialized to {counter}")
    return counter


def simulate_cell_2(counter: int) -> int:
    """
    Simulates Jupyter 'Cell 2': in a real notebook, this cell could
    reference 'counter' directly from kernel memory without it being
    passed in explicitly. In a plain script, that persistence does NOT
    exist between separate function calls unless the value is explicitly
    passed or stored in a shared/global scope.
    """
    counter += 5
    print(f"[Cell 2] counter incremented to {counter}")
    return counter


def simulate_cell_3(counter: int) -> None:
    """Simulates Jupyter 'Cell 3': final state reporting."""
    print(f"[Cell 3] final counter value: {counter}")


if __name__ == "__main__":
    # In a script, we must explicitly thread the value through each
    # function call — there is no implicit shared kernel memory as
    # there would be between executed cells in a Jupyter notebook.
    value_after_cell_1 = simulate_cell_1()
    value_after_cell_2 = simulate_cell_2(value_after_cell_1)
    simulate_cell_3(value_after_cell_2)
```

### Expected Terminal Output
```
[Cell 1] counter initialized to 10
[Cell 2] counter incremented to 15
[Cell 3] final counter value: 15
```

### Step-by-Step Code Explanation
1. Each `simulate_cell_N()` function stands in for a Jupyter notebook cell — in a real notebook, variables defined in one cell remain available in the kernel's memory for every cell executed afterward, in any order, even out of their original top-to-bottom sequence.
2. In this **script**, that persistence does not exist automatically: `simulate_cell_2` cannot see `counter` from `simulate_cell_1` unless it is explicitly passed as a parameter — demonstrated by threading `value_after_cell_1` into the next call.
3. This distinction is a common source of bugs when students prototype logic in Jupyter (relying on leftover cell-state) and then copy that code into a `.py` script, where the implicit persistence silently disappears, causing `NameError` exceptions.
4. The `if __name__ == "__main__":` guard shows the standard script-mode execution order: top-to-bottom, once, with explicit data flow between function calls.
5. Recognizing this conceptual gap is directly useful for the PCAP exam's emphasis on variable scope and program state — a notebook's implicit cross-cell state is not a Python language feature at all, but an artifact of the Jupyter kernel's execution model.
