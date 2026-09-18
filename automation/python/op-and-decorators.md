# OOP and Decorators

## Task 1 — Class and constructor

Created a `TestCase` class to represent a single test result — a "blueprint" that lets me build test objects with real data instead of loose variables floating around.

```python
class TestCase:
    def __init__(self, name, status, duration):
        self.name = name
        self.status = status
        self.duration = duration

test1 = TestCase("Bob", "PASSED", "0.200")
test2 = TestCase("Rik", "FAILED", "0.233")

print(test1.name, test1.status, test1.duration)
print(test2.name, test2.status, test2.duration)
```

**Output:**
```
Bob PASSED 0.200
Rik FAILED 0.233
```

**Reasoning:** `__init__` runs automatically when a new object is created and stores the passed-in values as attributes on `self`. `test1` and `test2` are two separate objects built from the same class, each holding its own data.

---

## Task 2 — Class method

Added a method that checks the test's status.

```python
def is_passed(self):
    if self.status == "PASSED":
        return True
    else:
        return False
```

```python
print(test1.is_passed())
print(test2.is_passed())
```

**Output:**
```
True
False
```

**Reasoning:** a method is just a function that lives inside a class, with `self` as its first parameter. `self` refers to whichever object called the method, so `self.status` reads that specific object's own `status` — same object, called through two different instances, two different results.

---

## Task 3 — Another method + returning a formatted string

```python
def summary(self):
    return f"Test '{self.name}' - {self.status} ({self.duration})"
```

```python
print(test1.summary())
print(test2.summary())
```

**Output:**
```
Test 'Bob' - PASSED (0.200)
Test 'Rik' - FAILED (0.233)
```

**Reasoning:** used `return` instead of printing inside the method, so the method just hands the string back to whoever called it — the caller decides what to do with it (print it, save it, compare it, etc.), instead of the method deciding for you.

---

## Task 4 — Inheritance

Created `APITestCase`, a subclass of `TestCase` that adds one more attribute — `endpoint` — on top of everything `TestCase` already has.

```python
class APITestCase(TestCase):
    def __init__(self, name, status, duration, endpoint):
        super().__init__(name, status, duration)
        self.endpoint = endpoint

test3 = APITestCase("Karl", "FAILED", "0.323", "23.2321.23")
print(test3.name, test3.status, test3.duration, test3.endpoint)
```

**Output:**
```
Karl FAILED 0.323 23.2321.23
```

**Reasoning:** `super().__init__(...)` calls the parent class's constructor, so `name`, `status`, and `duration` get set exactly like they do in `TestCase`, without duplicating that code. I only had to write the logic for the new part — `self.endpoint`.

---

## Task 5 — Polymorphism

Overrode `summary()` inside `APITestCase` so API tests print differently from plain tests:

```python
def summary(self):
    return f"[API] '{self.name}' - {self.status} ({self.duration}) @ {self.endpoint}"
```

```python
tests = (test1, test2, test3)

for t in tests:
    print(t.summary())
```

**Output:**
```
Test 'Bob' - PASSED (0.200)
Test 'Rik' - FAILED (0.233)
[API] 'Karl' - FAILED (0.323) @ 23.2321.23
```

**Reasoning:** this is polymorphism in practice — the loop calls `.summary()` on every object the same way, but Python picks the right version of the method depending on the object's actual class. `test1`/`test2` use the version defined in `TestCase`, `test3` uses the one overridden in `APITestCase` — same call, different behavior, no `if` checks needed to tell them apart.