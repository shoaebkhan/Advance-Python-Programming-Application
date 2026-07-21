# Week 1 — Day 3: Virtual Environments & PIP Package Management

**PCAP-31-03 Curriculum Domain Mapping:** Module 1 — Basics of the Python Language
*(1.1 Introduction to Python: the Python Standard Library vs. third-party (PyPI) packages, the `import` mechanism and `sys.path`, and foundational tooling knowledge that supports later Module 3 topics on modules/packages)*

---

## Topic Overview

A **virtual environment** is an isolated Python installation — a self-contained directory with its own interpreter reference and its own `site-packages` folder — that lets a project depend on specific package versions without affecting the system-wide (global) Python installation or other projects. Python's built-in `venv` module (Standard Library since Python 3.3) creates these environments with a single command: `python -m venv myenv`. Once activated (`source myenv/bin/activate` on macOS/Linux, `myenv\Scripts\activate` on Windows), any `pip install` command installs packages *only* into that environment's isolated folder, leaving the global Python untouched. This solves the classic "dependency hell" problem: Project A needing `requests==2.25.0` and Project B needing `requests==2.31.0` can coexist without conflict, each in its own virtual environment.

**PIP** ("Pip Installs Packages") is Python's standard package manager, used to install, upgrade, and remove packages from the **Python Package Index (PyPI)** — the public repository hosting hundreds of thousands of third-party libraries. Common commands include `pip install package_name`, `pip install package_name==1.2.3` (pinning an exact version), `pip freeze > requirements.txt` (exporting exact installed versions for reproducibility), and `pip install -r requirements.txt` (installing everything listed in that file — the standard way to recreate an environment on another machine or CI server). Understanding the distinction between the **Python Standard Library** (ships with every Python installation, no `pip install` needed — e.g., `os`, `sys`, `json`, `math`) and **third-party packages** (must be explicitly installed — e.g., `requests`, `numpy`, `flask`) is foundational, since the PCAP exam's Module 1 and Module 3 both assume familiarity with what `import` can access without any installation step.

For the exam and for real project work, you should be comfortable explaining: why virtual environments exist (isolation, reproducibility, avoiding version conflicts); how to verify programmatically whether code is currently running inside a virtual environment versus the global interpreter (a `sys.prefix` vs. `sys.base_prefix` comparison, demonstrated below); and how `sys.path` determines where `import` statements look for both Standard Library and installed third-party modules — directly connecting today's tooling topic back to yesterday's interpreter internals.

---

## Example 1 (Normal)

### Problem Statement
Write a script that programmatically detects whether it is currently running inside an active virtual environment or the global (system) Python interpreter, and reports which.

### Python Solution
```python
"""
venv_detector.py
Detects whether the current interpreter is running inside a virtual
environment by comparing sys.prefix to sys.base_prefix.
"""
import sys


def is_running_in_virtual_environment() -> bool:
    """
    Determine whether the current Python process is running inside
    a virtual environment.

    Returns:
        bool: True if inside a virtual environment, False if using
              the global/base interpreter.
    """
    # sys.base_prefix points to the "real" system Python installation.
    # sys.prefix points to wherever the CURRENT interpreter is installed.
    # Inside a venv, these two paths differ; outside one, they match.
    return sys.prefix != sys.base_prefix


def main() -> None:
    """Report the current environment status."""
    if is_running_in_virtual_environment():
        print("Status: Running inside a VIRTUAL ENVIRONMENT")
        print(f"Environment path: {sys.prefix}")
    else:
        print("Status: Running in the GLOBAL (system) interpreter")
        print(f"System interpreter path: {sys.prefix}")


if __name__ == "__main__":
    main()
```

### Expected Terminal Output
Inside an activated virtual environment:
```
Status: Running inside a VIRTUAL ENVIRONMENT
Environment path: /home/student/projects/myenv
```
Outside any virtual environment:
```
Status: Running in the GLOBAL (system) interpreter
System interpreter path: /usr
```

### Step-by-Step Code Explanation
1. `sys.base_prefix` always refers to the original, system-level Python installation's root directory, regardless of whether a virtual environment is active.
2. `sys.prefix` refers to whichever interpreter is *actually* being used to run the current code — inside a venv, this points to the venv's own directory instead.
3. `is_running_in_virtual_environment()` compares these two values: equal means no virtual environment is active; different means one is.
4. This is the exact technique used internally by many real-world tools (including `pip` itself) to warn users when they're about to install packages globally instead of into an intended isolated environment.
5. Running this script both with and without an activated venv is the clearest way to observe the difference described in the Topic Overview — the code doesn't change, only the environment it runs in does.

---

## Example 2 (Normal-Intermediate)

### Problem Statement
Write a script that uses the `importlib.metadata` module (Standard Library since Python 3.8) to list all currently installed third-party packages and their exact versions, without shelling out to `pip` directly.

### Python Solution
```python
"""
installed_packages_report.py
Lists all installed packages and their versions using importlib.metadata,
the Standard Library's programmatic interface to package metadata.
"""
from importlib.metadata import distributions


def list_installed_packages() -> list:
    """
    Build a sorted list of (package_name, version) tuples for every
    package installed in the current environment.

    Returns:
        list: Sorted list of (name, version) tuples.
    """
    packages = [
        (distribution.metadata["Name"], distribution.version)
        for distribution in distributions()
    ]
    return sorted(packages, key=lambda pair: pair[0].lower())


def print_package_report(packages: list, limit: int = 5) -> None:
    """
    Print a formatted report of the first N installed packages.

    Args:
        packages (list): List of (name, version) tuples.
        limit (int): Maximum number of packages to display.
    """
    print(f"Total installed packages found: {len(packages)}")
    print(f"Showing first {min(limit, len(packages))}:")
    for name, version in packages[:limit]:
        print(f"  - {name}=={version}")


if __name__ == "__main__":
    installed = list_installed_packages()
    print_package_report(installed)
```

### Expected Terminal Output
```
Total installed packages found: 14
Showing first 5:
  - certifi==2024.2.2
  - charset-normalizer==3.3.2
  - idna==3.6
  - pip==24.0
  - requests==2.31.0
```
*(Exact packages, versions, and total count will vary depending on what's installed in your environment.)*

### Step-by-Step Code Explanation
1. `from importlib.metadata import distributions` imports a Standard Library function (no third-party dependency needed) that yields metadata objects for every installed distribution package.
2. The list comprehension extracts each package's `Name` from its metadata and its `version` attribute, building `(name, version)` tuples.
3. `sorted(packages, key=lambda pair: pair[0].lower())` sorts alphabetically, case-insensitively, so output ordering is predictable and readable.
4. `print_package_report()` demonstrates a defensive pattern: `min(limit, len(packages))` prevents claiming to show 5 items if fewer than 5 actually exist.
5. This example is a programmatic equivalent of running `pip list` from the terminal — useful when you need package information *inside* a running Python program (e.g., a diagnostic tool or automated environment-compatibility checker) rather than as a manual terminal command.

---

## Example 3 (Intermediate)

### Problem Statement
Write a script that uses the `subprocess` module to safely check whether a specific package is installed by attempting to import it, and if missing, prints (but does not silently auto-execute) the exact `pip install` command needed.

### Python Solution
```python
"""
dependency_checker.py
Checks whether a given package can be imported; if not, reports the
exact pip command needed rather than installing anything automatically.
"""
import importlib
import sys


def check_dependency(package_name: str, import_name: str = None) -> bool:
    """
    Check whether a package is importable in the current environment.

    Args:
        package_name (str): The name used with 'pip install'.
        import_name (str): The name used with 'import', if it differs
            from package_name (e.g., 'Pillow' installs but imports as 'PIL').
            Defaults to package_name if not provided.

    Returns:
        bool: True if the package can be imported, False otherwise.
    """
    module_name = import_name if import_name else package_name
    try:
        importlib.import_module(module_name)
        return True
    except ImportError:
        return False


def report_dependency_status(packages: list) -> None:
    """
    Check a list of (package_name, import_name) pairs and print a
    clear status report, including install instructions for missing ones.

    Args:
        packages (list): List of (package_name, import_name) tuples.
    """
    missing_packages = []

    for package_name, import_name in packages:
        installed = check_dependency(package_name, import_name)
        status = "INSTALLED" if installed else "MISSING"
        print(f"[{status}] {package_name}")
        if not installed:
            missing_packages.append(package_name)

    if missing_packages:
        print("\nTo install missing packages, run:")
        print(f"  pip install {' '.join(missing_packages)}")
    else:
        print("\nAll checked dependencies are installed.")


if __name__ == "__main__":
    dependencies_to_check = [
        ("requests", "requests"),
        ("numpy", "numpy"),
        ("nonexistent-demo-package", "nonexistent_demo_package"),
    ]
    report_dependency_status(dependencies_to_check)
```

### Expected Terminal Output
```
[INSTALLED] requests
[MISSING] numpy
[MISSING] nonexistent-demo-package

To install missing packages, run:
  pip install numpy nonexistent-demo-package
```
*(Actual INSTALLED/MISSING status depends entirely on what's present in your environment.)*

### Step-by-Step Code Explanation
1. `check_dependency()` uses `importlib.import_module()` inside a `try/except ImportError` block — attempting an import is the most reliable way to test availability, rather than parsing `pip list` text output.
2. The optional `import_name` parameter handles a real-world PCAP-relevant nuance: some packages have a **different** PyPI install name than their Python import name (the classic example being `Pillow`, installed via `pip install Pillow` but imported via `import PIL`).
3. `report_dependency_status()` loops through each dependency, checks it, and accumulates any missing ones into `missing_packages`.
4. Rather than automatically running `pip install` via `subprocess` (a potentially dangerous side effect to trigger silently), the script deliberately prints the **exact command** for a human to review and run — a responsible-tooling design choice worth noting explicitly.
5. `' '.join(missing_packages)` builds a single space-separated string so the suggested command can install every missing package in one line, matching real `pip install` syntax for multiple packages.

---

## Example 4 (Complex/Exam-Level)

### Problem Statement
Write a script that programmatically creates a new virtual environment using the Standard Library's `venv` module, and then verifies the environment was created successfully by checking for the expected interpreter file inside it.

### Python Solution
```python
"""
venv_creator.py
Programmatically creates a virtual environment using the venv module
and verifies its structure.
"""
import os
import sys
import venv


def create_virtual_environment(target_directory: str) -> None:
    """
    Create a new virtual environment at the given directory.

    Args:
        target_directory (str): Path where the venv should be created.
    """
    builder = venv.EnvBuilder(with_pip=True, clear=True)
    builder.create(target_directory)
    print(f"Virtual environment created at: {target_directory}")


def verify_environment_structure(target_directory: str) -> bool:
    """
    Check whether the expected interpreter executable exists inside
    the newly created virtual environment.

    Args:
        target_directory (str): Path to the virtual environment.

    Returns:
        bool: True if the interpreter file was found, False otherwise.
    """
    if sys.platform.startswith("win"):
        expected_interpreter = os.path.join(target_directory, "Scripts", "python.exe")
    else:
        expected_interpreter = os.path.join(target_directory, "bin", "python")

    exists = os.path.isfile(expected_interpreter)
    if exists:
        print(f"Verification passed: interpreter found at {expected_interpreter}")
    else:
        print(f"Verification failed: no interpreter found at {expected_interpreter}")
    return exists


if __name__ == "__main__":
    env_path = os.path.join(os.getcwd(), "demo_env")
    create_virtual_environment(env_path)
    verify_environment_structure(env_path)
```

### Expected Terminal Output
```
Virtual environment created at: /home/student/projects/demo_env
Verification passed: interpreter found at /home/student/projects/demo_env/bin/python
```
*(On Windows, the path uses `demo_env\Scripts\python.exe` instead.)*

### Step-by-Step Code Explanation
1. `venv.EnvBuilder(with_pip=True, clear=True)` configures the environment builder to include a bundled `pip` installation and to clear/overwrite any existing directory at the target path rather than failing if it already exists.
2. `builder.create(target_directory)` performs the actual environment creation — the Standard Library equivalent of running `python -m venv demo_env` from the terminal, but callable from within a running Python program (useful for setup automation scripts).
3. `verify_environment_structure()` handles the real-world detail that Windows and Unix-like systems place the interpreter in different subfolders (`Scripts\python.exe` vs. `bin/python`) — `sys.platform.startswith("win")` branches accordingly.
4. `os.path.isfile()` confirms the interpreter binary genuinely exists on disk, giving concrete proof the environment was built correctly rather than just trusting that `builder.create()` didn't raise an exception.
5. This example ties directly back to Example 1: after creating this environment, activating it and re-running `venv_detector.py` from Example 1 *inside* it would report `Status: Running inside a VIRTUAL ENVIRONMENT` — connecting environment creation to environment detection end-to-end.

---

## Example 5 (Complex/Exam-Level)

### Problem Statement
Write a script that inspects and compares the `sys.path` (module search path) between what would be expected in a global interpreter versus an active virtual environment, explaining how `site-packages` isolation actually works at the path-resolution level.

### Python Solution
```python
"""
site_packages_isolation.py
Inspects sys.path to reveal exactly where Python looks for installed
packages, demonstrating how virtual environment isolation works at
the underlying path-resolution level.
"""
import site
import sys


def find_site_packages_paths() -> list:
    """
    Return every 'site-packages' directory currently on the module
    search path.

    Returns:
        list: Paths containing 'site-packages'.
    """
    return [path for path in sys.path if "site-packages" in path]


def report_isolation_status() -> None:
    """Print a diagnostic report showing site-packages isolation."""
    site_packages_paths = find_site_packages_paths()

    print(f"Interpreter prefix (sys.prefix): {sys.prefix}")
    print(f"Base prefix (sys.base_prefix):  {sys.base_prefix}")
    print(f"\nActive site-packages location(s):")
    for path in site_packages_paths:
        print(f"  - {path}")

    in_virtual_env = sys.prefix != sys.base_prefix
    if in_virtual_env:
        print(
            "\nBecause this interpreter is running inside a virtual "
            "environment, only THIS environment's site-packages folder "
            "is searched for third-party imports — the global "
            "site-packages folder is excluded from sys.path entirely."
        )
    else:
        print(
            "\nThis interpreter is the global installation, so imports "
            "resolve against the system-wide site-packages folder shared "
            "by every project that does not use its own virtual environment."
        )


if __name__ == "__main__":
    report_isolation_status()
```

### Expected Terminal Output
Inside an activated virtual environment:
```
Interpreter prefix (sys.prefix): /home/student/projects/demo_env
Base prefix (sys.base_prefix):  /usr

Active site-packages location(s):
  - /home/student/projects/demo_env/lib/python3.12/site-packages

Because this interpreter is running inside a virtual environment, only THIS environment's site-packages folder is searched for third-party imports — the global site-packages folder is excluded from sys.path entirely.
```

### Step-by-Step Code Explanation
1. `find_site_packages_paths()` filters `sys.path` for any entry containing `"site-packages"` — the conventional folder name where `pip`-installed packages physically live.
2. Comparing this output between a global interpreter run and a virtual-environment run makes the isolation mechanism concrete: in a venv, `sys.path` contains **only** the venv's own `site-packages` folder, not the system's — this is *why* installing a package inside a venv has zero effect on other projects.
3. `site` (imported but used implicitly via Python's own startup process) is the Standard Library module responsible for populating `sys.path` with these platform- and environment-specific site-packages entries at interpreter startup, before your script's first line even runs.
4. The conditional explanation branch reinforces the conceptual "why" behind Example 1's `sys.prefix`/`sys.base_prefix` check, connecting today's five examples into a single coherent mental model: detection (Ex. 1) → inspection (Ex. 2) → dependency verification (Ex. 3) → creation (Ex. 4) → isolation mechanism (Ex. 5).
5. This level of understanding — *why* venvs isolate packages, not just *how* to activate one — is exactly the depth the PCAP-31-03 exam expects when it tests foundational tooling and import-resolution concepts indirectly through Module 1 and Module 3 questions.
