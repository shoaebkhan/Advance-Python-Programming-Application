# Week 1 — Day 4: Python Standard Library & Built-in Functions

**PCAP-31-03 Curriculum Domain Mapping:** Module 1 — Basics of the Python Language
*(1.1 Introduction to Python: built-in functions such as `len()`, `type()`, `isinstance()`, `input()`; the `math` and `random` modules explicitly named in the PCAP-31-03 syllabus; object identity via `id()` and the `is` operator)*

---

## Topic Overview

Every Python installation ships with two layers of functionality available without any `pip install`: **built-in functions** (always available in every scope, with no `import` required — e.g., `len()`, `type()`, `print()`, `range()`, `isinstance()`) and the **Standard Library** (a vast collection of modules that ship with Python but require an explicit `import` statement to use — e.g., `math`, `random`, `datetime`, `os`, `sys`, `json`). The PCAP-31-03 exam explicitly names the `math` and `random` modules in its syllabus, making fluency with their most common functions (`math.sqrt()`, `math.floor()`, `math.ceil()`, `math.pi`; `random.random()`, `random.randint()`, `random.choice()`, `random.seed()`) a near-guaranteed exam topic, alongside core built-ins like `len()`, `type()`, and `isinstance()`.

A frequently misunderstood but exam-relevant pair of concepts is **object identity vs. equality**: the `==` operator checks whether two objects have equal *values*, while the `is` operator checks whether two variable names refer to the exact *same object in memory* — a distinction made concrete via the built-in `id()` function, which returns a unique integer identifying an object's memory location for the duration of its lifetime. Python's behavior here has a well-known quirk tested on the PCAP exam: small integers (typically -5 to 256) and short strings are often **cached/interned** by CPython, meaning `a = 5; b = 5; a is b` often evaluates to `True` — but this is an implementation detail, not a language guarantee, and relying on `is` for value comparison of arbitrary numbers or strings is considered incorrect practice and a common exam trap.

For the exam, be ready to: predict exact return values and types from common built-ins given specific inputs; recall which `math`/`random` functions exist and their exact signatures (e.g., `random.randint(a, b)` is **inclusive** of both endpoints, while `random.randrange(a, b)` is **exclusive** of `b` — a frequently tested distinction); and correctly reason about `is` vs. `==` in code-tracing questions.

---

## Example 1 (Normal)

### Problem Statement
Write a script that demonstrates the core built-in functions `len()`, `type()`, and `isinstance()` across several different data types, printing each result clearly labeled.

### Python Solution
```python
"""
builtin_basics.py
Demonstrates len(), type(), and isinstance() across multiple data types.
"""

sample_values = [
    "Python",
    [1, 2, 3, 4],
    {"language": "Python", "year": 1991},
    (10, 20),
    42,
    3.14,
]

for value in sample_values:
    print(f"Value: {value!r}")
    print(f"  type(): {type(value)}")

    # len() only works on objects that define __len__ (sized containers/strings)
    try:
        print(f"  len(): {len(value)}")
    except TypeError:
        print("  len(): N/A (this type has no length)")

    print(f"  isinstance(value, str): {isinstance(value, str)}")
    print()
```

### Expected Terminal Output
```
Value: 'Python'
  type(): <class 'str'>
  len(): 6
  isinstance(value, str): True

Value: [1, 2, 3, 4]
  type(): <class 'list'>
  len(): 4
  isinstance(value, str): False

Value: {'language': 'Python', 'year': 1991}
  type(): <class 'dict'>
  len(): 2
  isinstance(value, str): False

Value: (10, 20)
  type(): <class 'tuple'>
  len(): 2
  isinstance(value, str): False

Value: 42
  type(): <class 'int'>
  len(): N/A (this type has no length)
  isinstance(value, str): False

Value: 3.14
  type(): <class 'float'>
  len(): N/A (this type has no length)
  isinstance(value, str): False
```

### Step-by-Step Code Explanation
1. `sample_values` deliberately mixes six different built-in types to exercise each function against varied input.
2. `type(value)` always succeeds and returns the exact class object — note it returns the **specific** type (`<class 'str'>`), not a string like `"str"`.
3. `len(value)` succeeds for `str`, `list`, `dict`, and `tuple` (returning character count, element count, key count, and element count respectively) but raises `TypeError` for `int` and `float`, since numbers have no defined length — the `try/except` demonstrates handling this gracefully.
4. `isinstance(value, str)` returns a boolean, correctly identifying only the first item as a string — this is the PCAP-preferred way to check types (over comparing `type(value) == str` directly), because `isinstance()` also correctly handles inheritance/subclasses.
5. The `!r` format specifier in the f-string calls `repr()` on the value, which is why the string prints with quotes (`'Python'`) while the report remains readable for all other types too.

---

## Example 2 (Normal-Intermediate)

### Problem Statement
Write a script that uses several functions and constants from the `math` module — explicitly named in the PCAP-31-03 syllabus — to solve a simple geometry problem: calculating a circle's circumference and area, then rounding results using both `math.floor()` and `math.ceil()`.

### Python Solution
```python
"""
math_module_demo.py
Demonstrates math module functions and constants explicitly covered
by the PCAP-31-03 syllabus.
"""
import math


def calculate_circle_properties(radius: float) -> tuple:
    """
    Calculate a circle's circumference and area.

    Args:
        radius (float): The circle's radius.

    Returns:
        tuple: (circumference, area) as floats.
    """
    circumference = 2 * math.pi * radius
    area = math.pi * radius ** 2
    return circumference, area


def main() -> None:
    """Compute and report circle properties with floor/ceil rounding."""
    radius = 4.5
    circumference, area = calculate_circle_properties(radius)

    print(f"Radius: {radius}")
    print(f"Circumference: {circumference:.4f}")
    print(f"Area: {area:.4f}")

    print(f"Circumference floored: {math.floor(circumference)}")
    print(f"Circumference ceiled: {math.ceil(circumference)}")
    print(f"Area floored: {math.floor(area)}")
    print(f"Area ceiled: {math.ceil(area)}")

    print(f"Square root of area: {math.sqrt(area):.4f}")


if __name__ == "__main__":
    main()
```

### Expected Terminal Output
```
Radius: 4.5
Circumference: 28.2743
Area: 63.6173
Circumference floored: 28
Circumference ceiled: 29
Area floored: 63
Area ceiled: 64
Square root of area: 7.9760
```

### Step-by-Step Code Explanation
1. `math.pi` is a module-level float constant (`3.14159...`) — the PCAP exam expects you to know it lives in the `math` module, not as a built-in.
2. `2 * math.pi * radius` and `math.pi * radius ** 2` compute circumference and area using standard geometric formulas.
3. `math.floor()` always rounds **down** toward negative infinity, and `math.ceil()` always rounds **up** toward positive infinity — both return `int`, unlike Python's built-in `round()`, which returns the same type as its input (or `int` when no second argument is given) and rounds to the *nearest* value using banker's rounding — a distinction the PCAP exam tests directly.
4. `math.sqrt()` computes a square root and always returns a `float`, even when the result is a whole number (e.g., `math.sqrt(64)` returns `8.0`, not `8`).
5. The `:.4f` format specifier limits displayed decimal places to four for readability, without altering the underlying stored precision of the calculated values.

---

## Example 3 (Intermediate)

### Problem Statement
Write a script demonstrating the `random` module's most commonly tested functions: `random.random()`, `random.randint()`, `random.randrange()`, and `random.choice()`, using `random.seed()` to make the output reproducible for demonstration purposes.

### Python Solution
```python
"""
random_module_demo.py
Demonstrates commonly tested random module functions with a fixed
seed for reproducible output.
"""
import random

# Seeding guarantees identical "random" results on every run — useful
# for testing and for this deterministic demonstration.
random.seed(42)

# random.random(): float in the half-open interval [0.0, 1.0)
float_value = random.random()
print(f"random.random(): {float_value}")

# random.randint(a, b): integer, INCLUSIVE of both a and b
dice_roll = random.randint(1, 6)
print(f"random.randint(1, 6): {dice_roll}")

# random.randrange(a, b): integer, INCLUSIVE of a, EXCLUSIVE of b
range_value = random.randrange(0, 10)
print(f"random.randrange(0, 10): {range_value}")

# random.choice(sequence): picks one element from a non-empty sequence
colors = ["red", "green", "blue", "yellow"]
chosen_color = random.choice(colors)
print(f"random.choice(colors): {chosen_color}")

# random.shuffle(list): shuffles a list IN PLACE, returns None
numbers = [1, 2, 3, 4, 5]
random.shuffle(numbers)
print(f"random.shuffle result: {numbers}")
```

### Expected Terminal Output
```
random.random(): 0.6394267984578837
random.randint(1, 6): 1
random.randrange(0, 10): 4
random.choice(colors): yellow
random.shuffle result: [3, 4, 2, 1, 5]
```
*(With `random.seed(42)` fixed, these exact values are reproducible on the same Python version/implementation — CPython's Mersenne Twister algorithm is deterministic given the same seed.)*

### Step-by-Step Code Explanation
1. `random.seed(42)` initializes the pseudo-random number generator to a known starting state — without this, every run would produce different values, making the "Expected Terminal Output" impossible to specify exactly.
2. `random.random()` always returns a `float` in `[0.0, 1.0)` — note `0.0` is possible, but `1.0` is never returned (half-open interval).
3. `random.randint(1, 6)` simulates a standard six-sided die — critically, **both** `1` and `6` are possible outputs, since `randint()` is inclusive on both ends, a frequent PCAP exam trap versus `randrange()`.
4. `random.randrange(0, 10)` can return any integer from `0` through `9` — `10` itself is **excluded**, mirroring the same inclusive-start/exclusive-stop convention as `range()`.
5. `random.choice()` requires a non-empty sequence and raises `IndexError` on an empty one; `random.shuffle()` is unusual among these in that it mutates its argument **in place** and returns `None` — printing `random.shuffle(numbers)` directly (instead of the `numbers` list afterward, as done here) would incorrectly print `None`, a well-known exam trap.

---

## Example 4 (Complex/Exam-Level)

### Problem Statement
Write a script using the `datetime` module to calculate the number of days between two dates and determine whether a given year is a leap year, combining `datetime` with `calendar.isleap()`.

### Python Solution
```python
"""
date_calculations.py
Uses the datetime and calendar modules to compute date differences
and check leap years.
"""
import calendar
from datetime import date


def days_between_dates(start_date: date, end_date: date) -> int:
    """
    Calculate the number of days between two dates.

    Args:
        start_date (date): The earlier date.
        end_date (date): The later date.

    Returns:
        int: Number of days between the two dates (absolute value).
    """
    delta = end_date - start_date
    return abs(delta.days)


def check_leap_year(year: int) -> bool:
    """
    Determine whether a given year is a leap year.

    Args:
        year (int): The year to check.

    Returns:
        bool: True if year is a leap year, False otherwise.
    """
    return calendar.isleap(year)


def main() -> None:
    """Demonstrate date difference calculation and leap year checking."""
    course_start = date(2026, 1, 5)
    course_end = date(2026, 3, 27)

    total_days = days_between_dates(course_start, course_end)
    print(f"Course duration: {total_days} days")
    print(f"Course duration in weeks: {total_days / 7:.1f}")

    for year in (2024, 2025, 2026, 2028, 2100):
        is_leap = check_leap_year(year)
        print(f"{year} is a leap year: {is_leap}")


if __name__ == "__main__":
    main()
```

### Expected Terminal Output
```
Course duration: 81 days
Course duration in weeks: 11.6
2024 is a leap year: True
2025 is a leap year: False
2026 is a leap year: False
2028 is a leap year: True
2100 is a leap year: False
```

### Step-by-Step Code Explanation
1. `date(2026, 1, 5)` constructs a `datetime.date` object using year, month, day — a common Standard Library type not part of the PCAP core syllabus by name, but useful for demonstrating built-in/module interaction beyond `math`/`random`.
2. Subtracting two `date` objects (`end_date - start_date`) returns a `timedelta` object, whose `.days` attribute gives the whole-number day difference; `abs()` guards against a negative result if the arguments were passed in the wrong order.
3. `calendar.isleap(year)` correctly applies the full Gregorian leap-year rule: divisible by 4, **except** centuries **unless** also divisible by 400 — which is exactly why `2100` (divisible by 4 and 100, but not 400) correctly reports `False` despite naively "looking like" a leap year.
4. The `for year in (2024, 2025, 2026, 2028, 2100):` loop deliberately includes this century-boundary edge case (`2100`) to demonstrate the full rule, not just the simple "divisible by 4" shortcut many learners mistakenly assume is sufficient.
5. This example demonstrates combining two separate Standard Library modules (`datetime` and `calendar`) in a single coherent script — a realistic reflection of how Standard Library modules are actually used together in practice, beyond isolated single-module examples.

---

## Example 5 (Complex/Exam-Level)

### Problem Statement
Write a script that demonstrates the difference between `==` (value equality) and `is` (identity comparison) using `id()`, including CPython's small-integer/short-string caching behavior, and explains why `is` should not be used for general value comparison.

### Python Solution
```python
"""
identity_vs_equality.py
Demonstrates == (equality) vs. is (identity), including CPython's
integer caching behavior — a classic PCAP-31-03 exam topic.
"""


def compare_values(label: str, value_a, value_b) -> None:
    """
    Print a comparison report for two values: equality, identity,
    and their id() values.

    Args:
        label (str): Description of the comparison being performed.
        value_a: First value.
        value_b: Second value.
    """
    print(f"--- {label} ---")
    print(f"value_a = {value_a!r}, value_b = {value_b!r}")
    print(f"value_a == value_b: {value_a == value_b}")
    print(f"value_a is value_b: {value_a is value_b}")
    print(f"id(value_a): {id(value_a)}, id(value_b): {id(value_b)}")
    print()


# Case 1: small integers -- CPython caches integers from -5 to 256
small_int_a = 100
small_int_b = 100
compare_values("Small cached integers", small_int_a, small_int_b)

# Case 2: large integers -- typically NOT cached, so identity often differs
large_int_a = 100000
large_int_b = 100000
compare_values("Large (uncached) integers", large_int_a, large_int_b)

# Case 3: two separately constructed lists with equal contents
list_a = [1, 2, 3]
list_b = [1, 2, 3]
compare_values("Separately created lists", list_a, list_b)

# Case 4: same object referenced by two names
list_c = list_a
compare_values("Same object, two names", list_a, list_c)
```

### Expected Terminal Output
```
--- Small cached integers ---
value_a = 100, value_b = 100
value_a == value_b: True
value_a is value_b: True
id(value_a): 140713118727304, id(value_b): 140713118727304

--- Large (uncached) integers ---
value_a = 100000, value_b = 100000
value_a == value_b: True
value_a is value_b: False
id(value_a): 140712933458224, id(value_b): 140712933458128

--- Separately created lists ---
value_a = [1, 2, 3], value_b = [1, 2, 3]
value_a == value_b: True
value_a is value_b: False
id(value_a): 140712933521920, id(value_b): 140712933522560

--- Same object, two names ---
value_a = [1, 2, 3], value_b = [1, 2, 3]
value_a == value_b: True
value_a is value_b: True
id(value_a): 140712933521920, id(value_b): 140712933521920
```
*(Exact `id()` integer values are memory addresses and will differ on every run/machine — only the True/False patterns and whether IDs match are guaranteed/reproducible.)*

### Step-by-Step Code Explanation
1. **Case 1** demonstrates CPython's small-integer caching: integers from -5 to 256 are pre-allocated and reused, so two variables independently assigned the value `100` actually point to the **same object in memory**, making `is` return `True` — but this is a CPython implementation detail, **not** part of the Python language specification, and other implementations (or even future CPython versions) are not required to behave this way.
2. **Case 2** uses `100000`, outside the cached range, and correctly shows `is` returning `False` even though `==` still returns `True` — proving the two variables hold *equal but distinct* integer objects at different memory addresses. This directly demonstrates why relying on `is` for numeric comparison is fragile and considered incorrect practice.
3. **Case 3** shows that mutable containers like lists are essentially **never** identity-cached — even with completely identical contents, two independently created lists are always distinct objects, so `is` correctly returns `False` while `==` (which compares contents element-by-element) returns `True`.
4. **Case 4** demonstrates true identity: `list_c = list_a` does not copy the list — it creates a second name pointing to the exact same list object, so both `==` and `is` return `True`, and both `id()` calls return the identical value.
5. The PCAP-31-03 exam frequently tests exactly this reasoning: "predict whether `is` or `==` returns `True`" for a given code snippet — this example's four cases cover the full spectrum of scenarios (cached, uncached, equal-but-distinct, and truly identical) the exam is likely to probe.
