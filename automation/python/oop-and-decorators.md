# OOP and Decorators

## Task 1 - Class and constructor

Created a `TestCase` class to represent a single test result - a "blueprint" that lets me build test objects with real data instead of loose variables floating around.

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

## Task 2 - Class method

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

**Reasoning:** a method is just a function that lives inside a class, with `self` as its first parameter. `self` refers to whichever object called the method, so `self.status` reads that specific object's own `status` - same object, called through two different instances, two different results.

---

## Task 3 - Another method + returning a formatted string

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

**Reasoning:** used `return` instead of printing inside the method, so the method just hands the string back to whoever called it - the caller decides what to do with it (print it, save it, compare it, etc.), instead of the method deciding for you.

---

## Task 4 - Inheritance

Created `APITestCase`, a subclass of `TestCase` that adds one more attribute - `endpoint` - on top of everything `TestCase` already has.

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

**Reasoning:** `super().__init__(...)` calls the parent class's constructor, so `name`, `status`, and `duration` get set exactly like they do in `TestCase`, without duplicating that code. I only had to write the logic for the new part - `self.endpoint`.

---

## Task 5 - Polymorphism

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

**Reasoning:** this is polymorphism in practice - the loop calls `.summary()` on every object the same way, but Python picks the right version of the method depending on the object's actual class. `test1`/`test2` use the version defined in `TestCase`, `test3` uses the one overridden in `APITestCase` - same call, different behavior, no `if` checks needed to tell them apart.

---

## Full final code (OOP)

```python
class TestCase:
    def __init__(self, name, status, duration):
        self.name = name
        self.status = status
        self.duration = duration

    def is_passed(self):
        if self.status == "PASSED":
            return True
        else:
            return False

    def summary(self):
        return f"Test '{self.name}' - {self.status} ({self.duration})"


class APITestCase(TestCase):
    def __init__(self, name, status, duration, endpoint):
        super().__init__(name, status, duration)
        self.endpoint = endpoint

    def summary(self):
        return f"[API] '{self.name}' - {self.status} ({self.duration}) @ {self.endpoint}"


test1 = TestCase("Bob", "PASSED", "0.200")
test2 = TestCase("Rik", "FAILED", "0.233")
test3 = APITestCase("Karl", "FAILED", "0.323", "23.2321.23")
tests = (test1, test2, test3)

print(test1.name, test1.status, test1.duration)
print(test2.name, test2.status, test2.duration)
print(test1.is_passed())
print(test2.is_passed())
print(test1.summary())
print(test2.summary())
print(test3.name, test3.status, test3.duration, test3.endpoint)

for t in tests:
    print(t.summary())
```

**Output:**
```
Bob PASSED 0.200
Rik FAILED 0.233
True
False
Test 'Bob' - PASSED (0.200)
Test 'Rik' - FAILED (0.233)
Karl FAILED 0.323 23.2321.23
Test 'Bob' - PASSED (0.200)
Test 'Rik' - FAILED (0.233)
[API] 'Karl' - FAILED (0.323) @ 23.2321.23
```

---

## Decorators

### Task 1 - Simple logging decorator

```python
def log_call(func):
    def wrapper(name):
        print("Running test...")
        func(name)
        print("Test finished")
    return wrapper

@log_call
def run_test(name):
    print(f"Test: {name}")

run_test("login_test")
```

**Output:**
```
Running test...
Test: login_test
Test finished
```

**Reasoning:** a decorator is a function that takes another function (`func`) and returns a new one (`wrapper`) that wraps it. `@log_call` above `run_test` is shorthand for `run_test = log_call(run_test)`. When `run_test(...)` is called, it's actually `wrapper(...)` that runs - printing a message, calling the real function through `func(name)`, then printing another message.

---

### Task 2 - Validator decorator (based on the video example)

```python
def check_status(func):
    def wrapper(status):
        if status == "PASSED" or status == "FAILED":
            func(status)
        else:
            print("Неизвестный статус")
    return wrapper

@check_status
def print_status(status):
    print(f"Status: {status}")

print_status("PASSED")
print_status("FAILED")
print_status("UNKNOWN")
```

**Output:**
```
Status: PASSED
Status: FAILED
Неизвестный статус
```

**Reasoning:** same idea as the video's `validator`/`open_url` example - the wrapper checks a condition on the argument before deciding whether to call the real function at all. If the status isn't recognized, `func` never runs and the wrapper prints an error message instead.

---

### Task 3 - Decorator that counts calls

```python
calls = 0

def count_calls(func):
    def wrapper(name):
        global calls
        calls += 1
        print(f"Call #{calls}")
        func(name)
    return wrapper

@count_calls
def run_test_counted(name):
    print(f"Test: {name}")

run_test_counted("login_test")
run_test_counted("logout_test")
run_test_counted("signup_test")
```

**Output:**
```
Call #1
Test: login_test
Call #2
Test: logout_test
Call #3
Test: signup_test
```

**Reasoning:** `calls` is declared outside any function so it persists between calls, instead of resetting each time like a normal local variable would. `global calls` inside `wrapper` tells Python to modify that outer variable instead of creating a new local one - without it, `calls += 1` would fail since a new local `calls` wouldn't have a starting value yet.

---

### Task 4 - One decorator, two different functions

```python
@log_call
def open_report(path):
    print(f"Opening report: {path}")

run_test("login_test")
open_report("report.pdf")
```

**Output:**
```
Running test...
Test: login_test
Test finished
Running test...
Opening report: report.pdf
Test finished
```

**Reasoning:** `log_call` doesn't care what the wrapped function actually does - it just wraps whatever gets passed to it. Applying the same decorator to `run_test` and `open_report` proves that: both get the same "Running test..." / "Test finished" wrapping around completely different inner logic.

---

### Task 5 - My own version of the video's example

```python
def file_check(func):
    def wrapper(path):
        if ".log" in path:
            func(path)
        else:
            print("Неверный формат файла")
    return wrapper

@file_check
def open_log(path):
    print(f"Opening log: {path}")

open_log("report.txt")
open_log("report.log")
```

**Output:**
```
Неверный формат файла
Opening log: report.log
```

**Reasoning:** direct copy of the `validator`/`open_url` pattern from the video, just with my own condition (`.log` in the path) instead of checking for a dot in a URL. Same structure: check first, only call the real function if the check passes.

---

## Full final code

```python
def log_call(func):
    def wrapper(name):
        print("Running test...")
        func(name)
        print("Test finished")
    return wrapper


def check_status(func):
    def wrapper(status):
        if status == "PASSED" or status == "FAILED":
            func(status)
        else:
            print("Неизвестный статус")
    return wrapper


calls = 0

def count_calls(func):
    def wrapper(name):
        global calls
        calls += 1
        print(f"Call #{calls}")
        func(name)
    return wrapper


def file_check(func):
    def wrapper(path):
        if ".log" in path:
            func(path)
        else:
            print("Неверный формат файла")
    return wrapper


@count_calls
def run_test_counted(name):
    print(f"Test: {name}")


@log_call
def open_report(path):
    print(f"Opening report: {path}")


@log_call
def run_test(name):
    print(f"Test: {name}")


@file_check
def open_log(path):
    print(f"Opening log: {path}")


@check_status
def print_status(status):
    print(f"Status: {status}")


print_status("PASSED")
print_status("FAILED")
print_status("UNKNOWN")

run_test("login_test")

run_test_counted("login_test")
run_test_counted("logout_test")
run_test_counted("signup_test")

open_report("report.pdf")

open_log("report.txt")
open_log("report.log")
```

**Output:**
```
Status: PASSED
Status: FAILED
Неизвестный статус
Running test...
Test: login_test
Test finished
Call #1
Test: login_test
Call #2
Test: logout_test
Call #3
Test: signup_test
Running test...
Opening report: report.pdf
Test finished
Неверный формат файла
Opening log: report.log
```