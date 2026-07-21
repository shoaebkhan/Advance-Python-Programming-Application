# Week 1 — Day 2: Installation & IDEs — VS Code, PyCharm, Jupyter

---

## Student Instructions

Today's exercises focus on the practical mechanics of running Python code correctly across different environments, and on two exam-critical fundamentals: the full `print()` signature and code commenting conventions. Complete each exercise in whichever IDE you've installed (VS Code, PyCharm, or a Jupyter environment), but pay attention to Exercise 5, which specifically asks you to compare behavior *across* environments. Save each solution as its own `.py` file (except where a notebook is explicitly required). No external packages are needed — only the Standard Library.

---

## Exercise 1 (Normal)

### Problem Statement / Scenario
Write a script `documented_calculator.py` containing a module-level docstring describing what the file does, at least two `#` inline comments explaining specific lines, and a short calculation (your choice, e.g., computing the area of a rectangle) that prints its result.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Output should be exactly one line reporting your calculated result.
- Comments and the docstring must not appear in the terminal output at all — they are for source-code readability only.

### Constraints, Edge Cases, or Starter Hints
- Remember: a docstring not assigned to a variable and not used as a function/class/module documentation string is still just an (ignored) string literal expression at runtime — it will not print anything on its own.
- Place your module docstring as the very first statement in the file, before any imports or code.
- Keep the calculation simple — the goal of this exercise is correct comment usage, not calculation complexity.

---

## Exercise 2 (Normal-Intermediate)

### Problem Statement / Scenario
Write a script `receipt_formatter.py` that prints a simple three-item "receipt" using `print()` calls with custom `sep` and `end` values so that: (a) item names and prices are separated by a colon and a space, and (b) all three receipt lines appear directly adjacent with no blank lines between them, followed by a "Thank you!" line with a normal newline.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Each item line should follow the pattern `ItemName: Price` (e.g., `Coffee: 3.50`).
- All item lines and the closing "Thank you!" line must be visually adjacent with no extra blank lines.
- You must use the `sep` and `end` keyword arguments explicitly at least twice each across your script — do not solve this only with f-strings and default `print()` behavior.

### Constraints, Edge Cases, or Starter Hints
- Recall the default values: `sep=' '` and `end='\n'`. You'll need to override at least one of them to hit the ": " separator requirement, and possibly `end` to control line adjacency.
- Test your output carefully character-by-character against the expected pattern — this is exactly the kind of precision the PCAP exam tests with `print()` questions.
- You do not need a loop for this exercise; three separate `print()` calls (plus one closing call) is sufficient.

---

## Exercise 3 (Intermediate)

### Problem Statement / Scenario
Write a script `config_from_args.py` that reads command-line arguments via `sys.argv` to accept a "username" and a "role" (e.g., run as `python config_from_args.py Aisha Admin`), then prints a formatted welcome message using those two values. Handle the case where the required arguments are missing.

### Expected Input/Output Specifications or Behavior
- When run with exactly two extra arguments (username, role), output a single formatted welcome line incorporating both.
- When run with the wrong number of arguments (too few or too many), print a clear usage message instead of crashing with an unhandled exception, e.g.:
  ```
  Usage: python config_from_args.py <username> <role>
  ```
- Do not use `try/except` for this — use a length check on `sys.argv` instead.

### Constraints, Edge Cases, or Starter Hints
- Remember `sys.argv[0]` is always the script name itself, not a user-supplied argument — plan your indexing/length checks accordingly.
- Test this exercise from an actual terminal command line, not just by "running" the file with no arguments inside an IDE's default Run button, to confirm your argument-count logic works correctly.
- Consider configuring your IDE's Run Configuration (VS Code `launch.json` "args", or PyCharm's Run Configuration "Parameters" field) to pass arguments, and confirm the behavior matches running from a plain terminal.

---

## Exercise 4 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `interpreter_diagnostic.py` that prints a small diagnostic report containing: the interpreter's exact executable path, the Python version, the current working directory, and the total number of entries in the module import search path (not the full list — just the count). Then write one sentence (as a comment) explaining a real bug that mismatched interpreter/working-directory settings could cause in an IDE.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Output must contain four clearly labeled lines: executable path, version, working directory, and search-path entry count.
- Your explanatory comment must describe a plausible, specific bug scenario (e.g., relating to a "module not found" error or a relative file path failing) — a generic statement like "it could cause errors" is insufficient.

### Constraints, Edge Cases, or Starter Hints
- You will need `sys.executable`, `sys.version` (or `platform.python_version()`), `os.getcwd()`, and `len(sys.path)`.
- Think carefully about the distinction between the folder your **script file** is saved in versus the **current working directory** the interpreter reports — they are frequently different, especially when an IDE is configured to run scripts from a project root rather than the file's own folder.
- Run this same script from two different starting locations (e.g., directly from your IDE's Run button, and from a terminal after `cd`-ing into a different folder) and compare the working directory output — note any difference you observe as part of your submission.

---

## Exercise 5 (Complex/Exam-Level)

### Problem Statement / Scenario
Create **two** files for this exercise: a Jupyter notebook (`.ipynb`) named `state_experiment.ipynb` with at least three separate cells, and a plain script `state_experiment.py` that attempts to replicate the same logic. In the notebook, define a variable in one cell and reference/modify it in a later cell relying on the kernel's persistent state — run the cells **out of their written order** at least once and note what happens. In the script version, attempt the same variable-sharing approach *without* using functions or explicit parameter-passing, and observe/report what error (if any) occurs.

### Expected Input/Output Specifications or Behavior
- The notebook must demonstrate at least one case where running cells out of their original top-to-bottom order changes the final printed result, compared to running them in order.
- The script version should demonstrate what happens if you rely on implicit variable "reuse" the way a notebook allows, but structure it incorrectly for standard script execution (this is meant to surface a genuine `NameError` or unexpected value — document what you actually observe).
- Submit both files along with a short written comparison (as comments in the `.py` file, or a markdown cell in the notebook) summarizing the key behavioral difference you observed.

### Constraints, Edge Cases, or Starter Hints
- This exercise is intentionally exploratory — there is no single "correct" output, only a correct *observation and explanation* of Jupyter's persistent-kernel model versus a script's strict top-to-bottom, single-pass execution model.
- If you don't have Jupyter installed locally, most IDEs (including VS Code, with the Jupyter extension) can open and run `.ipynb` files directly — installation itself is not required beyond what today's topic already covers.
- Pay close attention to what happens if you restart the notebook's kernel and only run a *later* cell without re-running an earlier one it depends on — this is one of the most common real-world Jupyter bugs and directly illustrates why the persistent state model differs fundamentally from script execution.
