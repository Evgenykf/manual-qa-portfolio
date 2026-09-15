# Python — Files, Exceptions, with...as

Practice exercises on reading/writing files, exception handling, and the `with` statement — with a QA-oriented framing for each task.

## Task 1 — Writing to a file

**Goal:** write data to a file on disk instead of keeping it only in memory.

```python
f = open("test_log.txt", "w")
f.write("Test run started\n")
f.close()
```

**Reasoning:**
- `open(..., "w")` creates the file if it doesn't exist, or wipes it clean if it does — "w" always starts from a blank file.
- First tried calling `.write()` and `.close()` as standalone lines without saving the result of `open()` to a variable first — that fails, since a method always needs to be called on something (`f.write()`, not just `.write()` floating on its own).
- `\n` inside the string is a newline character — without it, multiple `.write()` calls would run together on the same line instead of stacking as separate lines.

## Task 2 — Reading from a file

**Goal:** read back what was just written.

```python
r = open("test_log.txt", "r")
print(r.read())
r.close()
```

**Output:**
```
Test run started
```

**Reasoning:**
- "r" (read) mode won't let you write — it only lets you read existing content without changing it. Opening a file in "w" by mistake here would've wiped it instead of reading it.
- `.read()` returns the file's content as a string, which then gets printed like any other string.

## Task 3 — `with...as` and append mode

**Goal:** rewrite the write step using `with`, which closes the file automatically, and add a second line without erasing the first.

```python
with open("test_log.txt", "a") as f:
    f.write("Test 1: PASSED\n")
```

**Output (file content after both tasks 1 and 3):**
```
Test run started
Test 1: PASSED
```

**Reasoning:**
- `with` handles closing the file automatically once the indented block ends — no need for a separate `.close()` call, and it still closes properly even if an error happens inside the block.
- "a" (append) adds to the end of the file instead of overwriting it — using "w" here again would've deleted the first line before adding the second.

## Task 4 — Catching a `FileNotFoundError`

**Goal:** attempt to open a file that doesn't exist, and handle the resulting error instead of letting the program crash.

```python
try:
    open("no_such_file.txt", "r")
except FileNotFoundError:
    print("File not found!")
```

**Output:**
```
File not found!
```

**Reasoning:**
- Code inside `try` runs first; if it raises an error, Python jumps straight to `except` instead of crashing the whole program — anything left in `try` after the failing line gets skipped.
- `except` only catches the specific error type it names — this only reacts to `FileNotFoundError`, not any other kind of error.
- Relevant for automation: a test that clicks an element that might not be on the page, or parses an API response that might come back malformed, can use the same pattern instead of letting one unexpected case crash the whole test run.

## Task 5 — Catching a `ZeroDivisionError`

**Goal:** same pattern as Task 4, applied to a different error type.

```python
try:
    10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")
```

**Output:**
```
Cannot divide by zero
```

**Reasoning:**
- Division by zero has no mathematical result, so Python raises `ZeroDivisionError` the moment it tries to evaluate `10 / 0` — same mechanism as Task 4, just a different error type and no file involved.
- `try`/`except` doesn't decide in advance whether code is "safe" — it runs the code as normal and only reacts if something actually goes wrong at that specific line.