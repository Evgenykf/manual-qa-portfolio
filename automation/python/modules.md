# Modules

## Task 1 — Creating my own module

Moved the pass rate calculation function into a separate file `qa_helpers.py` so it can be reused across other files instead of copying the code every time.

```python
# qa_helpers.py

def calculate_pass_rate(success, total):
    percent = success / total * 100
    percent_rounded = round(percent, 2)
    return percent_rounded
```

**Reasoning:** nothing new logic-wise — same formula as in the functions topic. The only difference is it now lives in its own file, ready to be imported.

---

## Task 2 — Importing a function from my own module

```python
# task_modules.py

from qa_helpers import calculate_pass_rate

result = calculate_pass_rate(143, 150)
print(result)
```

**Output:**
```
95.33
```

**Reasoning:** `from qa_helpers import calculate_pass_rate` pulls the specific function directly by name, no module prefix needed — I just call `calculate_pass_rate(...)` like it was defined in the same file.

---

## Task 3 — Importing the whole module

Added a second function to `qa_helpers.py`:

```python
# qa_helpers.py

def format_status(is_passed):
    if is_passed:
        return "PASSED"
    else:
        return "FAILED"
```

Then imported the whole module, without `from`:

```python
# task_modules.py

import qa_helpers

status = qa_helpers.format_status(True)
print(status)

result = qa_helpers.calculate_pass_rate(143, 150)
print(result)
```

**Output:**
```
PASSED
95.33
```

**Reasoning:** the difference from the previous task is that with `import qa_helpers` there's no shortcut access to the functions — I have to prefix every call with the module name: `qa_helpers.format_status(...)`. At first I didn't get why bother with this when `from ... import` is shorter, but it's really just two different import styles, both valid depending on the situation.

---

## Task 4 — Built-in `random` module

```python
import random

print(random.randint(1, 100))
```

**Output:**
```
41
```

**Reasoning:** `random.randint(1, 100)` generates a random integer between 1 and 100 inclusive. Treated it as a test's "execution time" in milliseconds — a new number every run.

---

## Task 5 — Built-in `datetime` module

```python
import datetime

print(datetime.datetime.now())
```

**Output:**
```
2026-09-17 08:22:05.209322
```

**Reasoning:** `datetime.datetime.now()` — the double `datetime` isn't a typo: the first is the module itself (the file), the second is the `datetime` class inside it, and `.now()` is a method of that class returning the current date and time. Useful as a timestamp for test logs.