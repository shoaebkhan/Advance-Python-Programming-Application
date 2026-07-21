# Week 1 — Day 5: OOP Overview — Procedural vs. Object-Oriented Mindset

---

## Student Instructions

This week closes with your first hands-on exposure to classes and objects — treat this as a conceptual on-ramp, not a full OOP deep-dive (inheritance, polymorphism, and dunder methods return in force in Weeks 8–9). Focus each exercise on **contrasting** procedural and object-oriented thinking for the same problem, and pay close attention to Exercise 5, which tests a genuinely tricky distinction (class vs. instance attributes) that appears frequently on the PCAP exam. Save each solution as its own `.py` file. No external packages are required.

---

## Exercise 1 (Normal)

### Problem Statement / Scenario
Solve the same problem two ways in one file, `temperature_converter_both_styles.py`: first write a plain function `celsius_to_fahrenheit(celsius)` that returns the converted value, then write a minimal class `TemperatureReading` that stores a Celsius value at construction and provides a method `to_fahrenheit()` returning the same conversion.

### Expected Input/Output Specifications or Behavior
- No user input required — use a hardcoded Celsius value (e.g., 25) for both approaches.
- Both the procedural function and the class method must produce the identical numeric Fahrenheit result for the same input value.
- Print both results clearly labeled so a reader can confirm they match.

### Constraints, Edge Cases, or Starter Hints
- Recall the formula: `F = (C * 9/5) + 32`.
- Your class's `__init__` should store the Celsius value as an instance attribute (`self.celsius = ...`); your `to_fahrenheit()` method should use `self.celsius` rather than requiring the value to be passed in again.
- Keep this exercise intentionally simple — the goal is comparing the two *structures*, not building complex conversion logic.

---

## Exercise 2 (Normal-Intermediate)

### Problem Statement / Scenario
Write a script `inventory_both_styles.py` that tracks three products (name and price) two ways: first using two parallel lists (procedural), then using a `Product` class and a list of `Product` objects (OOP). For both approaches, print a formatted report of all three products and their prices, then deliberately reorder or remove one item from only ONE of the two data structures in the procedural version (e.g., remove one name but forget to remove its matching price) and observe/report the resulting mismatch.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Both procedural and OOP reports must correctly print all product names with their correct matching prices in their initial (correct) state.
- After deliberately desynchronizing the procedural parallel lists, print the resulting (now-incorrect) report and add a comment identifying exactly which product is now mismatched with the wrong price.
- The OOP version, since each `Product` object is self-contained, should have no equivalent way to accidentally cause this exact bug — demonstrate this by removing a `Product` object cleanly from the OOP list and showing the report remains correct.

### Constraints, Edge Cases, or Starter Hints
- This exercise is designed to make you deliberately break the procedural version to observe the exact failure mode discussed in the SOLVED examples — don't skip the "break it on purpose" step, it's the point of the exercise.
- Your `Product` class needs at minimum a `name` and `price` instance attribute and a method that returns a formatted description string.
- Removing an item from a Python list uses `.remove(value)` or `del list[index]` — make sure you understand which one you're using and why, for both the parallel-list and object-list removal steps.

---

## Exercise 3 (Intermediate)

### Problem Statement / Scenario
Write a class `Playlist` that manages a collection of song titles (as plain strings, not objects) added by a user, with methods `add_song(title)`, `remove_song(title)`, and `show_songs()`. Create **two separate** `Playlist` instances and prove they maintain fully independent song collections — adding a song to one playlist must never appear in the other.

### Expected Input/Output Specifications or Behavior
- No user input required — call your methods directly with hardcoded song titles in your script's main block.
- After adding different songs to two separate `Playlist` instances, printing each playlist's contents must show only that playlist's own songs — zero overlap unless a song was explicitly added to both.
- `remove_song()` on a title not present in that playlist should not crash — handle this gracefully with a clear message.

### Constraints, Edge Cases, or Starter Hints
- Just like the `Library` class's `self.books = []` from the SOLVED examples, your `Playlist.__init__` must create a fresh list **inside** `__init__` using `self.songs = []` — not as a class-level attribute outside `__init__`, or you will accidentally share one list across every `Playlist` instance (test this explicitly if you're unsure why it matters).
- Use Python's `in` operator to check membership before attempting removal, or wrap the removal in a way that handles a missing title without raising an unhandled exception.
- Demonstrate independence explicitly in your output: add different songs to each playlist, then print both playlists' contents side by side so the lack of overlap is directly visible.

---

## Exercise 4 (Complex/Exam-Level)

### Problem Statement / Scenario
Model a simple "Classroom" composed of "Student" objects (composition, like the SOLVED `Library`/`Book` example). Your `Classroom` class should support adding students, marking a specific student "present" or "absent" by name, and printing an attendance report. Each `Student` object should track its own name and attendance status independently.

### Expected Input/Output Specifications or Behavior
- No user input required — hardcode at least four students added to one classroom.
- Marking one student present/absent must not affect any other student's status.
- The attendance report must clearly list every student's name alongside their current status (Present/Absent), with a sensible default status for newly added students (your choice — document your default in a comment).
- Attempting to mark attendance for a student name that doesn't exist in the classroom should print a clear error message, not crash.

### Constraints, Edge Cases, or Starter Hints
- Follow the same composition pattern as the SOLVED `Library` example: `Classroom` should hold a list of `Student` objects in an instance attribute created fresh inside `__init__`.
- Think about how your `mark_attendance(name, status)` method should search through the classroom's student list to find the matching student by name — what should happen if two students happen to share the same name? (You don't need to solve this edge case fully, but note it as a comment acknowledging the limitation.)
- Test your "student not found" error path explicitly by attempting to mark attendance for a name you know isn't in your classroom, and confirm your script handles it gracefully.

---

## Exercise 5 (Complex/Exam-Level)

### Problem Statement / Scenario
Write a class `GameCharacter` with a **class attribute** `game_title` (shared across all characters) and **instance attributes** `name` and `health` (unique per character). Demonstrate, through a sequence of print statements and deliberate modifications, all three of the following distinct scenarios: (1) reading the shared class attribute from multiple instances before any changes, (2) changing the class attribute at the class level and observing the effect on all existing instances, and (3) overriding the attribute on just ONE instance and confirming only that instance is affected afterward. Before each print statement in your script, add a one-line comment predicting the output.

### Expected Input/Output Specifications or Behavior
- No user input required.
- Create at least two `GameCharacter` instances.
- Your output must clearly demonstrate all three scenarios described above, with clear labeling so a reader can follow which scenario each block of output corresponds to.
- Include a `__str__` method on `GameCharacter` so that `print(character)` shows a clean, human-readable summary of the character's name, health, and current game_title value (rather than Python's default object representation).

### Constraints, Edge Cases, or Starter Hints
- This exercise mirrors the SOLVED `Employee` example's structure closely on purpose — do not simply copy it; use a different theme (game characters) and write your own predictions to prove you understand *why* each result occurs, not just *that* it occurs.
- Pay very close attention to the exact syntax difference between `GameCharacter.game_title = "New Value"` (changes the shared class attribute) and `some_instance.game_title = "New Value"` (creates a new instance attribute that shadows the class attribute for that object only) — this is the single most exam-relevant detail in this entire exercise.
- After completing the exercise, write a 2–3 sentence comment at the bottom of your file explaining, in your own words, why this distinction matters when designing real classes — specifically, what kind of data is a genuinely good candidate for a class attribute versus an instance attribute.
