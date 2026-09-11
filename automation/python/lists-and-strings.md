# Python — Lists & Strings (Indexing, Slicing)

Practice exercises on lists, string indexing/slicing, and string methods — with a QA-oriented framing for each task.

## Task 1 — Lists: indexing, adding, removing

**Goal:** work with a list of test results — access elements by index, add a new result, remove one.

```python
test_results = ["PASSED", "FAILED", "PASSED", "PASSED", "SKIPPED"]

print(test_results[0])
print(test_results[-1])

test_results.append("FAILED")
test_results.remove("SKIPPED")
print(test_results)
```

**Output:**
```
PASSED
SKIPPED
['PASSED', 'FAILED', 'PASSED', 'PASSED', 'FAILED', 'FAILED']
```

**Reasoning:**
- Indexing starts at `0`, not `1` — `[0]` is the first element, `[-1]` is always the last one regardless of list length.
- `append()` and `remove()` are methods called on the list itself (`list.method()`), same pattern as string methods used later in this file.

## Task 2 — Lists: length, membership, sorting

**Goal:** check a list's size, check if a value exists in it, and sort it.

```python
execution_times = [1.2, 0.5, 3.8, 0.9, 2.1]

execution_times.sort()
print(execution_times)
print(len(execution_times))
print(max(execution_times))
print(min(execution_times))
print(3.8 in execution_times)
```

**Output:**
```
[0.5, 0.9, 1.2, 2.1, 3.8]
5
3.8
0.5
True
```

**Reasoning:**
- `sort()` changes the list in place — it doesn't return a new list, so it has to be called on its own line rather than wrapped in `print()`.
- `in` checks membership without needing a loop — same keyword used in `for x in list`, but here it's just a standalone True/False check.

## Task 3 — Strings: indexing and slicing

**Goal:** get individual characters and substrings from a string using indexes and slices.

```python
test_name = "Login with valid credentials"

print(test_name[0])
print(test_name[-1])
print(len(test_name))
print(test_name[0:5])
print(test_name[-5:])
print(test_name[6:10])
```

**Output:**
```
L
s
29
Login
ntial
with
```

**Reasoning:**
- Strings support the same indexing as lists — `[0]`/`[-1]` work exactly the same way.
- A slice like `[a:b]` moves left to right, so `[-5:0]` doesn't work — the end index has to come after the start index in the string's actual order. Leaving the end empty (`[-5:]`) means "go to the end of the string," which isn't the same as writing `0`.
- `[6:10]` — the end index isn't included, so this grabs 4 characters (positions 6, 7, 8, 9).

## Task 4 — String methods: cleaning and checking content

**Goal:** clean up a messy string and check what it contains.

```python
raw_status = "  FAILED  "
print(raw_status.strip().lower())

test_name = "Login with valid credentials"
print("Login" in test_name)
print(test_name.startswith("Login"))
```

**Output:**
```
failed
True
True
```

**Reasoning:**
- Chained `.strip().lower()` — `.strip()` runs first and its result gets passed straight into `.lower()`, no need for an intermediate variable.
- `in` checks if a substring appears anywhere in the string; `startswith()` is stricter — it only matches if the string begins with that exact substring.

## Task 5 — Splitting a path string

**Goal:** split a file path string into parts and grab the last one.

```python
test_path = "tests/api/login_test.py"
print(test_path.split("/"))
print(test_path.split("/")[-1])
```

**Output:**
```
['tests', 'api', 'login_test.py']
login_test.py
```

**Reasoning:**
- `split("/")` turns the string into a list — from there it's just a regular list, so `[-1]` works the same as it did with `test_results` in Task 1.
- Avoided slicing the raw string by hand (e.g. counting characters from the end) — that only works for one specific string length and breaks the moment the path changes.

## Bonus Task — Parsing a test log line

**Goal:** combine everything above — clean, split, and re-split a raw log line into its separate parts, without using loops or conditionals.

```python
log_line = "  tests/api/login_test.py::test_valid_login - PASSED  "

log = log_line.strip()
parts = log.split(" - ")
parts2 = parts[0].split("::")
parts3 = parts2[0].split("/")

print(parts3[-1])
print(parts2[1])
print(parts[1])
```

**Output:**
```
login_test.py
test_valid_login
PASSED
```

**Reasoning:**
- Split the problem into stages instead of trying to do it in one line: strip whitespace first, then split off the status, then split the remaining path+test-name string again by `::`, then split the file path by `/`.
- Each `split()` had to be applied to the *result* of the previous step, not back to the original string — reusing the same variable name for a new `split()` call overwrote the earlier result and broke the chain, had to keep each step in its own variable.
- Used `[-1]` instead of a hardcoded index like `[2]` for the filename — `[2]` only happens to work because this specific path has exactly 3 parts; `[-1]` works no matter how deep the folder structure is.