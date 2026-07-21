# Week 1 — Day 3: Virtual Environments & PIP Package Management

---

## Student Instructions

Today's exercises require you to actually create and interact with a real virtual environment on your machine — reading about `venv` and `pip` is not a substitute for using them. Before starting, confirm you can run `python -m venv --help` and `pip --help` successfully from your terminal. For exercises involving package installation, use small, well-known packages (e.g., `requests`) so installation is fast. Submit each script as its own `.py` file, and where instructed, also submit the generated `requirements.txt` or environment folder listing as evidence of your work.

---

## Exercise 1 (Normal)

### Problem Statement / Scenario
Create a new virtual environment named `lab_env` from the terminal (not programmatically — use the command line for this exercise). Then write a script `env_check.py` that, when run *inside* the activated `lab_env`, prints a clear confirmation message stating it is running inside a virtual environment, along with the environment's path.

### Expected Input/Output Specifications or Behavior
- No user input required.
- When run with `lab_env` activated, output must clearly state the environment is active and show its filesystem path.
- Run the same script again with `lab_env` deactivated (or in a fresh terminal without activation) and confirm the output correctly reports the opposite status.

### Constraints, Edge Cases, or Starter Hints
- Use the `sys.prefix` vs. `sys.base_prefix` comparison technique — do not attempt to detect this by checking environment variable names or folder names as strings, since that approach is fragile and not portable across operating systems.
- Submit both terminal outputs (activated and deactivated) as evidence, not just the script.
- Remember the activation command differs by OS: `source lab_env/bin/activate` (macOS/Linux) vs. `lab_env\Scripts\activate` (Windows).

---

## Exercise 2 (Normal-Intermediate)

### Problem Statement / Scenario
Inside your activated `lab_env`, install the `requests` package via `pip`. Then write a script `package_reporter.py` that uses `importlib.metadata` to confirm `requests` is installed and print its exact version number, along with the total count of all packages currently installed in the environment.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Output must include a line explicitly confirming `requests` is installed with its version number.
- Output must include a separate line reporting the total number of installed packages found.
- If `requests` is somehow not found, the script must print a clear "not found" message rather than crashing with an unhandled exception.

### Constraints, Edge Cases, or Starter Hints
- Use `importlib.metadata.distributions()` or `importlib.metadata.version()` — do not shell out to `pip show` via `subprocess` for this exercise; the goal is practicing the programmatic metadata API.
- Consider what exception type `importlib.metadata.version()` raises if a package name isn't found, and handle it explicitly.
- Compare your total package count between `lab_env` and your global interpreter (run the same script without activation) — you should observe a meaningfully smaller number inside the fresh virtual environment.

---

## Exercise 3 (Intermediate)

### Problem Statement / Scenario
Write a script `multi_dependency_audit.py` that checks a list of **five** package names of your choosing (mix of packages you know are installed and at least two you know are not) and produces a clean audit report showing installed/missing status for each, plus a single consolidated `pip install ...` command line for anything missing.

### Expected Input/Output Specifications or Behavior
- No user input required — hardcode your list of five packages to check inside the script.
- Output must clearly label each package as installed or missing.
- If any packages are missing, the final output line must be a single valid `pip install` command listing all of them space-separated.
- If none are missing, print a clear "all dependencies satisfied" message instead.

### Constraints, Edge Cases, or Starter Hints
- Use `importlib.import_module()` inside a `try/except ImportError` block to test each package — this is more reliable than trying to parse text from a `pip list` command.
- Remember that some packages install under one name but import under a different name (research at least one real example of this mismatch and include it in your test list, with a comment noting which package it is and why the names differ).
- Do not actually run any installation commands automatically from within your script — only print the suggested command for a human to review.

---

## Exercise 4 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `project_bootstrapper.py` that automates the first step of setting up a new project: it should programmatically create a new virtual environment at a path provided as a command-line argument (e.g., `python project_bootstrapper.py new_project_env`), then verify the environment was created successfully by checking for the interpreter executable inside it, and finally print next-step instructions for the user (the exact activation command for their OS).

### Expected Input/Output Specifications or Behavior
- Accept exactly one command-line argument: the desired environment folder name/path. If missing, print a usage message and exit without crashing.
- After creation, print a clear success or failure message based on whether the expected interpreter file actually exists on disk.
- Print the correct activation command for the operating system the script is currently running on (don't just print both Windows and Unix commands unconditionally — detect and print only the relevant one).

### Constraints, Edge Cases, or Starter Hints
- Use the Standard Library `venv` module's `EnvBuilder` (or the simpler `venv.create()` function) — no `subprocess` calls to the `venv` command-line tool are needed since a full Python API exists.
- Use `sys.platform` to detect the operating system and branch your activation-command message and your expected-interpreter-path check accordingly (recall: Windows uses a `Scripts` folder, Unix-like systems use a `bin` folder).
- Test your script by actually running the environment it creates afterward — activate it and confirm `pip` works correctly inside it, as evidence your bootstrapper produced a genuinely functional environment, not just an empty folder.

---

## Exercise 5 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `isolation_proof.py` that provides concrete, printed proof of virtual environment isolation: it should install a small test package (your choice, e.g., `requests`) inside an activated virtual environment, then demonstrate that the **global** interpreter (outside that environment) cannot import it, while the **virtual environment's** interpreter can. Structure this as a two-part exercise combining a script and a written comparison report.

### Expected Input/Output Specifications or Behavior
- Run the same diagnostic script twice: once using the virtual environment's Python interpreter, once using the global Python interpreter, without modifying the script between runs.
- The script's output must clearly state whether the target package (e.g., `requests`) was successfully imported or not, in each run.
- Submit both terminal outputs side by side (or clearly labeled) along with a 2–3 sentence written explanation of *why* the same package name resolves differently depending on which interpreter executed the script — referencing `sys.path` specifically in your explanation.

### Constraints, Edge Cases, or Starter Hints
- This exercise assumes your chosen test package is installed **only** inside the virtual environment, not globally — if it happens to already be installed globally too, choose a different, more obscure package so the isolation is actually observable.
- Use `try/except ImportError` in your diagnostic script rather than `importlib.metadata`, since the goal here is demonstrating actual import behavior, not just metadata presence.
- Think about how you would invoke the *same script file* with two *different* interpreters from the terminal — you do not need to duplicate the script; only the interpreter executable path used to run it changes between your two test runs (e.g., `lab_env/bin/python isolation_proof.py` vs. plain `python isolation_proof.py` or `python3 isolation_proof.py` for the global one).
