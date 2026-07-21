# Week 1 — Day 1: Introduction to Python — History, Interpreters vs. Compilers

---

## Student Instructions

Today's exercises reinforce your understanding of how Python executes code — the difference between interactive and script mode, how the interpreter processes a file sequentially, and how to use Standard Library introspection tools to examine the interpreter itself. Work through each exercise **without running to a solution first** — write your own script, test it, and compare its behavior against the "Expected Input/Output Specifications" listed. Save each solution as its own `.py` file. You will need only the Python Standard Library (no `pip install` required) for all five exercises. Where a traceback is expected, run your script and copy the **full** traceback into your submission — do not paraphrase it.

---

## Exercise 1 (Normal)

### Problem Statement / Scenario
Write a script named `system_report.py` that prints a short "system report" containing: (a) a fixed title line, (b) the operating system name Python is running on, and (c) the exact Python version string.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Output must contain exactly three lines, in this order:
  1. A title line of your choosing (e.g., `=== System Report ===`)
  2. A line reporting the OS platform
  3. A line reporting the full Python version string
- Example output shape (values will differ per machine):
  ```
  === System Report ===
  Operating System: Linux
  Python Version: 3.12.3 (main, ...)
  ```

### Constraints, Edge Cases, or Starter Hints
- You will need two Standard Library modules — one for OS/platform info, one for version info. Consider `platform` and `sys`.
- Look up `platform.system()` and `sys.version` in the official Python documentation before writing code.
- Do not hardcode the OS name or version — the script must retrieve them programmatically so it works correctly on any machine.

---

## Exercise 2 (Normal-Intermediate)

### Problem Statement / Scenario
Create a script called `mode_comparison.py` that contains three bare arithmetic expressions (no `print()` around them) followed by the same three expressions wrapped in `print()`. Run the file and observe the output, then add a comment above each section explaining, in your own words, why the two sections behave differently in script mode.

### Expected Input/Output Specifications or Behavior
- No user input required.
- When run as a script, only the `print()`-wrapped expressions should produce visible terminal output; the bare expressions should produce none.
- Your submitted file must include your written comment explaining the behavior — this is graded as part of the exercise, not just the code.

### Constraints, Edge Cases, or Starter Hints
- This exercise is about observation and explanation, not complex logic — keep the three expressions simple (e.g., basic arithmetic or string concatenation).
- Think about what would happen if you typed the same bare expressions one at a time into the interactive shell (`python` with no filename) instead of running a `.py` file — how would the output differ, and why?
- Do not use any imports for this exercise.

---

## Exercise 3 (Intermediate)

### Problem Statement / Scenario
Write a script `bytecode_lab.py` that defines **two** small functions of your own design (e.g., one that multiplies two numbers, one that checks if a number is even) and uses the `dis` module to print the bytecode for both. Then, in a comment at the bottom of the file, note at least one instruction name (opcode) that appears in both disassembly outputs and explain what you believe that instruction does.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Output should clearly label which disassembly belongs to which function (e.g., print a header line before each `dis.dis()` call).
- Output will show CPython bytecode instructions such as `LOAD_FAST`, `RETURN_VALUE`, `BINARY_OP`, etc. — exact instruction names may vary by your installed Python version.

### Constraints, Edge Cases, or Starter Hints
- Import the `dis` module — no installation needed, it's part of the Standard Library.
- Keep both functions short (1–3 lines of logic) so the bytecode output is easy to read and compare.
- If you're unsure what an opcode means, the official Python documentation has a full "dis — Disassembler" reference page listing every bytecode instruction.

---

## Exercise 4 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `cache_investigator.py` that: (a) writes a new small module file to disk containing at least one function and one constant, (b) imports that module, (c) checks whether a `__pycache__` directory now exists in the same folder, and (d) if it exists, prints the name of the `.pyc` file found inside it. Your script should handle the case where, for some reason, no cache file was generated (e.g., print a clear message instead of crashing).

### Expected Input/Output Specifications or Behavior
- No user input required.
- On a successful run, output should include: confirmation the module was created, confirmation it was imported successfully (e.g., by printing something from it), and either the `.pyc` filename found or a clear "no cache found" message.
- The script must not crash if `__pycache__` doesn't exist — this must be handled gracefully, not left to raise an unhandled exception.

### Constraints, Edge Cases, or Starter Hints
- You'll need file-writing (`open()` with write mode), the `os` module for directory/path operations, and `sys.path` manipulation so Python can find your newly created module.
- Consider: what Standard Library function lets you check if a directory exists before trying to list its contents?
- Think about why simply running your **script itself** twice does *not* produce a `.pyc` file for the script — but importing a separate **module** does. (Hint: this relates to how `__main__` is treated differently from imported modules.)

---

## Exercise 5 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `execution_trace.py` containing at least **five** sequential `print()` statements, where the **fourth** statement deliberately triggers a runtime error of your choosing (for example, accessing an invalid list index, converting an invalid string to an integer, or dividing by zero). Run the script and observe exactly which lines print before the crash.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Print statements 1–3 must appear in the terminal output before the crash occurs.
- The error you trigger must produce a full Python traceback ending in a specific, named exception (e.g., `ZeroDivisionError`, `ValueError`, `IndexError`).
- Print statement 5 (after the error) must **never** appear in the output.
- In a comment at the top of your file, state which exception type you expect to be raised and why.

### Constraints, Edge Cases, or Starter Hints
- Do **not** use a `try/except` block for this exercise — the goal is to observe an *unhandled* exception and its traceback. Exception handling is covered in a later week.
- Choose an error that is guaranteed to occur every time the script runs (avoid anything relying on random values or external input) so your output is fully reproducible.
- After running your script, copy the complete traceback text (not a summary) into your submission, and identify in a comment which line number the traceback reports as the failure point.
