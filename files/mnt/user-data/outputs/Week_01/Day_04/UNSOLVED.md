# Week 1 — Day 4: Python Standard Library & Built-in Functions

---

## Student Instructions

Today's exercises drill the built-in functions and Standard Library modules (`math`, `random`, and one module of your own exploration) that are most directly tested on the PCAP-31-03 exam. Precision matters here — several exercises ask you to predict or verify *exact* behavior (inclusive vs. exclusive ranges, return types, rounding direction). Where randomness is involved, use `random.seed()` so your output is reproducible and gradable. No external packages are required — only the Standard Library.

---

## Exercise 1 (Normal)

### Problem Statement / Scenario
Write a script `type_explorer.py` that accepts five hardcoded values of your own choosing (covering at least four different data types — for example a string, a list, an integer, a float, and a boolean) and, for each, prints its type using `type()`, whether it is an instance of `int` using `isinstance()`, and its length using `len()` where applicable.

### Expected Input/Output Specifications or Behavior
- No user input required — define your five values directly in the script.
- For each value, print its type, its `isinstance(value, int)` result, and its length (or a clear "no length" message if `len()` is not applicable).
- The script must not crash on any value, including ones where `len()` would normally raise `TypeError`.

### Constraints, Edge Cases, or Starter Hints
- Remember `bool` is technically a subclass of `int` in Python — think carefully about what `isinstance(True, int)` should report, and confirm your script's output matches your prediction before submitting.
- Use a `try/except TypeError` block around your `len()` call to handle values where it doesn't apply.
- Include at least one container type (list, tuple, or dict) and at least one non-container type (int, float, or bool) in your five values.

---

## Exercise 2 (Normal-Intermediate)

### Problem Statement / Scenario
Write a script `geometry_toolkit.py` that calculates the volume and surface area of a sphere given its radius, using at least three different `math` module members (a function and a constant at minimum), then reports both the raw floating-point results and their floor/ceiling integer values.

### Expected Input/Output Specifications or Behavior
- No user input required — hardcode a radius value of your choosing.
- Output must show: the raw (unrounded, formatted to a reasonable number of decimal places) volume and surface area, plus the floored and ceiled integer version of each.
- You must use `math.pi`, and at least one of `math.floor()` or `math.ceil()` for each calculated value (both, ideally).

### Constraints, Edge Cases, or Starter Hints
- Sphere volume formula: `(4/3) * π * r³`. Sphere surface area formula: `4 * π * r²`.
- Recall that `math.floor()` and `math.ceil()` both return `int`, while the raw formula results are `float` — make sure your printed output type-formatting reflects this correctly (don't accidentally format an int with float-style decimal places).
- Test with a radius that produces a non-whole-number result so the floor/ceil difference is actually visible in your output (a whole-number result would make floor and ceil identical, hiding the distinction you're meant to demonstrate).

---

## Exercise 3 (Intermediate)

### Problem Statement / Scenario
Write a script `dice_simulator.py` that simulates rolling two six-sided dice 10 times using `random.randint()`, records each roll pair and their sum, and reports how many times the sum was exactly 7 (statistically the most common outcome for two dice). Seed the random generator so your results are reproducible.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Use `random.seed()` with a fixed value of your choosing at the top of the script.
- Print each of the 10 rolls in the format `Roll N: die1 + die2 = sum`.
- Print a final summary line stating how many of the 10 rolls summed to exactly 7.

### Constraints, Edge Cases, or Starter Hints
- Use `random.randint(1, 6)` — remember this function is inclusive of both `1` and `6`; using `random.randrange()` with the wrong bounds here would silently produce an incorrect simulation (e.g., never rolling a 6).
- Use a loop (`for _ in range(10):`) rather than writing out 10 separate roll blocks manually.
- Double-check your seed value stays fixed across runs — if your output changes each time you run the script, your seeding is incorrect or misplaced (it must be called once, before any `random` calls, not inside the loop).

---

## Exercise 4 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `age_calculator.py` that, using the `datetime` module, calculates a person's exact age in years given their birth date (hardcoded) and today's date (retrieved programmatically, not hardcoded), correctly accounting for whether their birthday has already occurred this calendar year.

### Expected Input/Output Specifications or Behavior
- No user input required — hardcode a birth date of your choosing using `datetime.date(year, month, day)`.
- Use `date.today()` to get the current date programmatically — do not hardcode "today's" date.
- Output must print the calculated exact age in whole years, correctly adjusted (i.e., simply subtracting birth year from current year is **not** sufficient if the birthday hasn't occurred yet this year — your script must handle this correctly).

### Constraints, Edge Cases, or Starter Hints
- A naive `today.year - birth_date.year` calculation over-counts by one for anyone whose birthday later in the calendar year hasn't happened yet — think about how to compare `(month, day)` tuples to detect this and subtract 1 when needed.
- Test your logic explicitly against a birth date where the birthday **has** already passed this year, and a second birth date where it has **not** — include both cases in your script and confirm both produce a correctly adjusted result.
- You do not need the `calendar` module for this exercise, but you may find it useful to cross-check your leap-year edge cases (e.g., a birth date of February 29th) if you want an extra challenge.

---

## Exercise 5 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a script `identity_lab.py` that constructs your own set of at least **four** distinct test cases (beyond the ones shown in class) exploring `is` vs. `==`, including at least one case involving strings (not just integers or lists), and at least one case specifically designed to produce a **surprising** result for someone who doesn't understand CPython's caching behavior. For each case, print a clear prediction comment **before** the code runs, then the actual result, so a reader can check their own understanding against the real output.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Each of your four (or more) test cases must include a comment stating your prediction of the `is` result **before** the corresponding print statement — written as if explaining it to a classmate who hasn't studied this yet.
- Output must clearly show, for each case: the two values, the `==` result, the `is` result, and both `id()` values.
- At least one case must involve string comparison (research and test whether short string literals exhibit similar caching/interning behavior to small integers).

### Constraints, Edge Cases, or Starter Hints
- Consider testing: two identical short string literals defined separately, two identical strings built dynamically (e.g., via concatenation or `str()` conversion at runtime rather than as literal text in your source code), and see whether the results differ — this touches on Python's string interning behavior, a nuanced extension of the integer caching concept covered in class.
- Remember the core principle to defend in writing (as a comment at the bottom of your file): why is `is` considered unreliable for general-purpose value comparison, even in cases where it happens to "work" due to caching? Your answer should explain that caching behavior is a CPython implementation detail, not a language guarantee.
- Do not simply copy the four cases already shown in the Day 4 SOLVED examples — construct genuinely new scenarios to demonstrate your own understanding.
