# Week 1 — Day 5: OOP Overview — Procedural vs. Object-Oriented Mindset

**PCAP-31-03 Curriculum Domain Mapping:** Module 4 — Object-Oriented Programming (Preview)
*(4.1 The Object-Oriented Approach: classes, objects, attributes, methods, the role of `self`, and the distinction between class attributes and instance attributes — introduced conceptually here, formalized in depth in Weeks 8–9)*

---

## Topic Overview

**Procedural programming** organizes a program as a sequence of function calls that operate on data passed between them — data and behavior are separate. **Object-oriented programming (OOP)** instead bundles data (called **attributes**) and the behavior that operates on that data (called **methods**) together into a single unit called an **object**, created from a **class** blueprint. This is not a superficial syntax difference — it's a different way of modeling a problem: procedural code asks "what functions do I need, and what data do I pass them?" while OOP asks "what *things* exist in this problem, and what do they know and do?" Python supports both paradigms fully and simultaneously, so the choice is about *fit*, not about one being universally superior.

A Python class is defined with the `class` keyword, and every instance method's first parameter is conventionally named `self` — a reference to the specific object the method was called on, automatically passed by Python whenever you call `instance.method()`. The special `__init__` method (a "dunder"/"magic" method, covered more deeply in Week 9) runs automatically when a new object is created via `ClassName(...)`, and is where instance **attributes** are typically initialized using `self.attribute_name = value`. Each object created from a class has its **own independent copy** of instance attributes — changing one object's attribute never affects another object of the same class — which is precisely the behavior procedural code must manage manually (e.g., via separate dictionaries or parallel data structures) without a language-level construct to enforce it.

For the PCAP exam, be ready to: read a simple class definition and correctly trace what happens when methods are called on different instances; understand that `self` inside a method refers to the specific object the method is bound to; and distinguish an **instance attribute** (defined via `self.x = ...`, unique per object) from a **class attribute** (defined directly in the class body, shared by *all* instances unless individually overridden) — a distinction this Day's final example demonstrates directly, since it is a well-documented PCAP exam trap.

---

## Example 1 (Normal)

### Problem Statement
Solve the same simple problem — representing a rectangle and calculating its area — two ways: first procedurally using a plain function, then using a minimal class, to make the structural difference concrete.

### Python Solution
```python
"""
procedural_vs_oop_basic.py
Solves the same problem (rectangle area) procedurally, then with a
minimal class, to contrast the two paradigms directly.
"""

# --- Procedural approach ---
def calculate_rectangle_area(width: float, height: float) -> float:
    """
    Calculate the area of a rectangle.

    Args:
        width (float): Rectangle width.
        height (float): Rectangle height.

    Returns:
        float: The calculated area.
    """
    return width * height


# --- Object-Oriented approach ---
class Rectangle:
    """Represents a rectangle with a width and height."""

    def __init__(self, width: float, height: float):
        """
        Initialize a Rectangle instance.

        Args:
            width (float): Rectangle width.
            height (float): Rectangle height.
        """
        self.width = width
        self.height = height

    def calculate_area(self) -> float:
        """Return this rectangle's area."""
        return self.width * self.height


if __name__ == "__main__":
    # Procedural: data (5, 3) and behavior (the function) are separate
    procedural_area = calculate_rectangle_area(5, 3)
    print(f"Procedural result: {procedural_area}")

    # OOP: data (width, height) and behavior (calculate_area) live together
    my_rectangle = Rectangle(5, 3)
    oop_area = my_rectangle.calculate_area()
    print(f"OOP result: {oop_area}")
```

### Expected Terminal Output
```
Procedural result: 15
OOP result: 15
```

### Step-by-Step Code Explanation
1. The procedural version is a plain function: it accepts `width` and `height` as parameters every single time it's called — the function itself holds no memory of any particular rectangle.
2. The OOP version's `__init__` method stores `width` and `height` as **instance attributes** (`self.width`, `self.height`) at object creation time — the object now *remembers* its own dimensions.
3. `my_rectangle.calculate_area()` needs no arguments beyond the implicit `self`, because the object already has access to its own stored `width` and `height` — contrast this with the procedural function, which must be re-given both values on every call.
4. Both approaches produce the identical numeric result (`15`) — proving the paradigm choice here is about **code organization**, not about one being more "correct" for this simple case.
5. The real advantage of the OOP approach becomes clear once you need to track *many* rectangles at once, or add more rectangle-related behavior later (perimeter, scaling, comparison) — a limitation the next example demonstrates directly.

---

## Example 2 (Normal-Intermediate)

### Problem Statement
Extend the previous comparison to a scenario with **multiple** items — tracking three students' names and grades — showing how procedural code requires manually parallel data structures while OOP naturally groups related data per object.

### Python Solution
```python
"""
managing_multiple_items.py
Demonstrates why OOP scales more naturally than procedural code when
tracking multiple related items (three students and their grades).
"""

# --- Procedural approach: parallel lists, manually kept in sync ---
student_names = ["Ayesha", "Bilal", "Sara"]
student_grades = [88, 92, 79]


def procedural_report() -> None:
    """Print a report using manually paired parallel lists."""
    print("--- Procedural Report ---")
    for index in range(len(student_names)):
        print(f"{student_names[index]}: {student_grades[index]}")


# --- Object-Oriented approach: each student is a self-contained object ---
class Student:
    """Represents a single student with a name and a grade."""

    def __init__(self, name: str, grade: int):
        """
        Initialize a Student instance.

        Args:
            name (str): Student's name.
            grade (int): Student's numeric grade.
        """
        self.name = name
        self.grade = grade

    def describe(self) -> str:
        """Return a formatted description of this student."""
        return f"{self.name}: {self.grade}"


def oop_report(students: list) -> None:
    """
    Print a report using a list of Student objects.

    Args:
        students (list): List of Student instances.
    """
    print("--- OOP Report ---")
    for student in students:
        print(student.describe())


if __name__ == "__main__":
    procedural_report()

    students = [
        Student("Ayesha", 88),
        Student("Bilal", 92),
        Student("Sara", 79),
    ]
    oop_report(students)
```

### Expected Terminal Output
```
--- Procedural Report ---
Ayesha: 88
Bilal: 92
Sara: 79
--- OOP Report ---
Ayesha: 88
Bilal: 92
Sara: 79
```

### Step-by-Step Code Explanation
1. The procedural approach relies on **two separate lists** (`student_names`, `student_grades`) that must always stay perfectly synchronized by index — inserting, removing, or reordering one list without the other silently corrupts the data with no error raised.
2. The OOP approach's `Student` class bundles a name and grade into a **single object** — there is no possibility of one student's name becoming mismatched with another's grade, because each object is self-contained.
3. `oop_report()` iterates a single list of `Student` objects, calling `.describe()` on each — no index-based cross-referencing between two collections is needed at all.
4. Both reports produce identical output here, but the OOP version's **data integrity guarantee** (name and grade can never become desynchronized) is a structural advantage the procedural version cannot offer without extra manual discipline.
5. This example sets up the motivation for Weeks 8–9's deeper OOP coverage: as the number of related attributes per "thing" grows (imagine adding email, enrollment date, and course list per student), the procedural parallel-lists approach becomes unmanageable quickly, while the class-based approach scales by simply adding more attributes to `__init__`.

---

## Example 3 (Intermediate)

### Problem Statement
Write a class `BankAccount` demonstrating that each instance maintains its own **independent** state — deposits and withdrawals on one account object must never affect a different account object of the same class.

### Python Solution
```python
"""
independent_object_state.py
Demonstrates that each object instance maintains fully independent state.
"""


class BankAccount:
    """Represents a simple bank account with a balance."""

    def __init__(self, owner_name: str, starting_balance: float = 0.0):
        """
        Initialize a BankAccount instance.

        Args:
            owner_name (str): The account owner's name.
            starting_balance (float): Initial balance (defaults to 0.0).
        """
        self.owner_name = owner_name
        self.balance = starting_balance

    def deposit(self, amount: float) -> None:
        """
        Add funds to this account's balance.

        Args:
            amount (float): Amount to deposit. Must be positive.
        """
        if amount <= 0:
            print(f"[{self.owner_name}] Deposit rejected: amount must be positive.")
            return
        self.balance += amount

    def withdraw(self, amount: float) -> None:
        """
        Remove funds from this account's balance, if sufficient.

        Args:
            amount (float): Amount to withdraw.
        """
        if amount > self.balance:
            print(f"[{self.owner_name}] Withdrawal rejected: insufficient funds.")
            return
        self.balance -= amount

    def report_balance(self) -> None:
        """Print this account's current balance."""
        print(f"[{self.owner_name}] Current balance: {self.balance:.2f}")


if __name__ == "__main__":
    account_alpha = BankAccount("Alpha Corp", 1000.0)
    account_beta = BankAccount("Beta LLC", 500.0)

    account_alpha.deposit(250.0)
    account_beta.withdraw(100.0)

    account_alpha.report_balance()
    account_beta.report_balance()

    # Prove independence: a large withdrawal on one account does not
    # affect the other account's balance at all.
    account_alpha.withdraw(5000.0)
    account_alpha.report_balance()
    account_beta.report_balance()
```

### Expected Terminal Output
```
[Alpha Corp] Current balance: 1250.00
[Beta LLC] Current balance: 400.00
[Alpha Corp] Withdrawal rejected: insufficient funds.
[Alpha Corp] Current balance: 1250.00
[Beta LLC] Current balance: 400.00
```

### Step-by-Step Code Explanation
1. `account_alpha` and `account_beta` are two entirely separate `BankAccount` objects, each with its own `self.balance` created independently when `__init__` ran for that specific object.
2. `account_alpha.deposit(250.0)` modifies **only** `account_alpha`'s `self.balance` — Python has no mechanism by which this could accidentally affect `account_beta`, since each object's attributes live in its own separate memory.
3. `account_beta.withdraw(100.0)` similarly only touches `account_beta`'s balance, dropping it from `500.0` to `400.0`.
4. The final `account_alpha.withdraw(5000.0)` deliberately exceeds Alpha's balance and is correctly rejected by the `if amount > self.balance:` guard — critically, this rejected operation has **zero** effect on `account_beta`, whose balance remains unchanged at `400.00`, definitively proving object state independence.
5. This independent-state guarantee is the core practical payoff of OOP that procedural code (Example 2's parallel-lists approach) cannot offer without significant extra manual bookkeeping — each `BankAccount` object is a fully self-contained unit of data and behavior.

---

## Example 4 (Complex/Exam-Level)

### Problem Statement
Write a class `Library` composed of multiple `Book` objects, demonstrating how OOP naturally models **relationships between objects** (a library *has* books) — something procedural code would need to represent as nested/parallel data structures manually.

### Python Solution
```python
"""
composed_objects.py
Demonstrates modeling a relationship between objects: a Library
containing multiple Book objects.
"""


class Book:
    """Represents a single book with a title and availability status."""

    def __init__(self, title: str, author: str):
        """
        Initialize a Book instance.

        Args:
            title (str): The book's title.
            author (str): The book's author.
        """
        self.title = title
        self.author = author
        self.is_checked_out = False

    def describe(self) -> str:
        """Return a formatted description including checkout status."""
        status = "checked out" if self.is_checked_out else "available"
        return f'"{self.title}" by {self.author} ({status})'


class Library:
    """Represents a library that holds a collection of Book objects."""

    def __init__(self, name: str):
        """
        Initialize a Library instance with an empty book collection.

        Args:
            name (str): The library's name.
        """
        self.name = name
        self.books = []  # Each Library instance owns its OWN book list

    def add_book(self, book: Book) -> None:
        """
        Add a Book object to this library's collection.

        Args:
            book (Book): The book to add.
        """
        self.books.append(book)

    def checkout_book(self, title: str) -> bool:
        """
        Mark a book as checked out by title, if found and available.

        Args:
            title (str): The title of the book to check out.

        Returns:
            bool: True if successfully checked out, False otherwise.
        """
        for book in self.books:
            if book.title == title and not book.is_checked_out:
                book.is_checked_out = True
                return True
        return False

    def list_books(self) -> None:
        """Print a description of every book in this library."""
        print(f"--- {self.name} Catalog ---")
        for book in self.books:
            print(book.describe())


if __name__ == "__main__":
    city_library = Library("Downtown Public Library")
    city_library.add_book(Book("Automate the Boring Stuff", "Al Sweigart"))
    city_library.add_book(Book("Fluent Python", "Luciano Ramalho"))

    city_library.list_books()

    checkout_success = city_library.checkout_book("Fluent Python")
    print(f"\nCheckout successful: {checkout_success}")
    city_library.list_books()
```

### Expected Terminal Output
```
--- Downtown Public Library Catalog ---
"Automate the Boring Stuff" by Al Sweigart (available)
"Fluent Python" by Luciano Ramalho (available)

Checkout successful: True
--- Downtown Public Library Catalog ---
"Automate the Boring Stuff" by Al Sweigart (available)
"Fluent Python" by Luciano Ramalho (checked out)
```

### Step-by-Step Code Explanation
1. `Book` and `Library` are two separate, cooperating classes — `Library` doesn't inherit from `Book` (inheritance is covered starting Week 8); instead, a `Library` object **contains** `Book` objects in its `self.books` list — a relationship pattern called **composition**.
2. `self.books = []` inside `Library.__init__` is critical: it must be created **fresh for each Library instance** inside `__init__`, not as a shared class-level list — otherwise every `Library` object would accidentally share the exact same book list (a well-known and exam-relevant Python pitfall explored further in Example 5).
3. `add_book()` appends `Book` objects into this instance's own list, and `checkout_book()` searches through them, mutating a specific `Book` object's `is_checked_out` attribute directly when a match is found.
4. `list_books()` calls `.describe()` on each contained `Book` object — the `Library` doesn't need to know *how* a book describes itself, only that it can ask each book to do so; this delegation of responsibility to the right object is a foundational OOP design habit.
5. The output confirms correct composed behavior: checking out `"Fluent Python"` updates only that specific book's status, leaving `"Automate the Boring Stuff"` untouched — modeling a real-world one-to-many relationship far more naturally than a procedural approach using parallel titles/authors/status lists would.

---

## Example 5 (Complex/Exam-Level)

### Problem Statement
Write a class demonstrating the critical distinction between **class attributes** (shared across all instances) and **instance attributes** (unique per instance) — a well-documented PCAP-31-03 exam trap — alongside a basic `__str__` dunder method preview showing how `print(object)` behaves by default versus when customized.

### Python Solution
```python
"""
class_vs_instance_attributes.py
Demonstrates the class-attribute vs. instance-attribute distinction,
plus a __str__ preview showing how print() displays objects.
"""


class Employee:
    """Represents an employee, demonstrating shared vs. per-instance data."""

    # CLASS ATTRIBUTE: defined directly in the class body.
    # Shared by ALL instances unless a specific instance overrides it.
    company_name = "Acme Corporation"

    def __init__(self, name: str, salary: float):
        """
        Initialize an Employee instance.

        Args:
            name (str): Employee's name.
            salary (float): Employee's salary.
        """
        # INSTANCE ATTRIBUTES: defined via self, unique to each object.
        self.name = name
        self.salary = salary

    def __str__(self) -> str:
        """
        Define how this object appears when passed to print() or str().
        Without this method, print(employee) would show something like
        <__main__.Employee object at 0x...> instead.
        """
        return f"{self.name} (${self.salary:,.2f}) — {self.company_name}"


if __name__ == "__main__":
    employee_one = Employee("Fatima Noor", 85000)
    employee_two = Employee("James Carter", 92000)

    # Class attribute is shared: both instances see the same company_name
    print(f"employee_one.company_name: {employee_one.company_name}")
    print(f"employee_two.company_name: {employee_two.company_name}")
    print(f"Same object? {employee_one.company_name is employee_two.company_name}")

    print()

    # Changing it on the CLASS affects every instance that hasn't overridden it
    Employee.company_name = "Acme Global Holdings"
    print(f"After class-level change:")
    print(f"employee_one.company_name: {employee_one.company_name}")
    print(f"employee_two.company_name: {employee_two.company_name}")

    print()

    # But assigning to ONE instance directly creates an INSTANCE attribute
    # that shadows the class attribute for that object ONLY.
    employee_one.company_name = "Independent Consulting LLC"
    print(f"After instance-level override on employee_one only:")
    print(f"employee_one.company_name: {employee_one.company_name}")
    print(f"employee_two.company_name: {employee_two.company_name}")

    print()

    # __str__ preview: print() now shows a custom, readable representation
    print(employee_one)
    print(employee_two)
```

### Expected Terminal Output
```
employee_one.company_name: Acme Corporation
employee_two.company_name: Acme Corporation
Same object? True

After class-level change:
employee_one.company_name: Acme Global Holdings
employee_two.company_name: Acme Global Holdings

After instance-level override on employee_one only:
employee_one.company_name: Independent Consulting LLC
employee_two.company_name: Acme Global Holdings

Fatima Noor ($85,000.00) — Independent Consulting LLC
James Carter ($92,000.00) — Acme Global Holdings
```

### Step-by-Step Code Explanation
1. `company_name = "Acme Corporation"` inside the class body (not inside `__init__`, and not prefixed with `self.`) is a **class attribute** — it belongs to the `Employee` class itself, and every instance accesses the exact same shared object, confirmed by `is` returning `True` initially.
2. `Employee.company_name = "Acme Global Holdings"` modifies the class attribute directly on the class — since neither instance has its own override yet, **both** `employee_one` and `employee_two` immediately reflect this change, because they were never storing their own separate copy.
3. `employee_one.company_name = "Independent Consulting LLC"` behaves completely differently: this creates a **brand-new instance attribute** on `employee_one` specifically, which now *shadows* (hides) the class attribute for that object only — `employee_two` is entirely unaffected and continues reading the shared class attribute.
4. This exact "shared until individually overridden, then permanently shadowed" behavior is one of the most commonly tested PCAP-31-03 OOP trace-the-output questions, precisely because it surprises learners who assume all `self.attribute` reads pull from the same shared source.
5. `__str__` is previewed here (formally covered as a dunder method in Week 9) to show that `print(employee_one)` now calls this custom method automatically instead of displaying Python's unhelpful default object representation (`<__main__.Employee object at 0x...>`) — note the final output correctly reflects `employee_one`'s overridden `company_name` and `employee_two`'s shared one, exactly matching the attribute distinction demonstrated above.
