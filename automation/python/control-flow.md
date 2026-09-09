# Python — Control Flow: Conditionals & Loops

Practice exercises on `if`/`elif`/`else`, logical operators, `for` loops, and `while` loops — with a QA-oriented framing for each task.

## Task 1 — Conditional branching on a status code

**Goal:** branch on an HTTP-style status code using `if`/`elif`/`else`.

```python
status_code = 404

if status_code == 200:
    print("OK")
elif status_code == 404:
    print("Not Found")
elif status_code == 500:
    print("Server Error")
else:
    print("Unknown status")
```

**Output:**
```
Not Found
```

**Reasoning:**
- First tried comparing `status_code` to string values (`"200"`, `"404"`) by mistake — same trap as comparing `"200" == 200` from the basics topic, just inside a condition this time. Fixed it by comparing to plain numbers.
- Python checks the conditions top to bottom and stops at the first match — it doesn't check the rest of the `elif` chain once one condition is true. Worth remembering if conditions could overlap (e.g. an exact match vs. a range check).

## Task 2 — Logical `and`

**Goal:** grant access only if both a username and a password match, using `and`.

```python
username = "admin"
password = "1234"

if username == "admin" and password == "1234":
    print("Access granted")
else:
    print("Access denied")
```

**Output:**
```
Access granted
```

**Reasoning:**
- `and` requires both conditions to be true — if either one fails, the whole check fails and falls through to `else`.
- This is the same pattern I'd use in a test to check multiple conditions before marking a test step as passed (e.g. correct status code **and** expected response body).

## Task 3 — Looping over a list with a counter

**Goal:** loop through a list of test results and print each one with a running test number.

```python
test_results = ["PASSED", "FAILED", "PASSED", "PASSED", "FAILED"]
test_number = 1

for result in test_results:
    print(f"Test {test_number}: {result}")
    test_number += 1
```

**Output:**
```
Test 1: PASSED
Test 2: FAILED
Test 3: PASSED
Test 4: PASSED
Test 5: FAILED
```

**Reasoning:**
- The list itself doesn't store test numbers, so I needed a separate counter variable outside the loop, incremented once per iteration.
- Kept `test_number += 1` inside the loop (same indentation as `print`) — if it were outside the loop it would only run once, after the loop ends, instead of on every pass.

## Task 4 — Counting results by condition

**Goal:** count how many results are `"PASSED"` vs `"FAILED"` using two separate counters inside a loop.

```python
test_results = ["PASSED", "FAILED", "PASSED", "PASSED", "FAILED"]
passed_count = 0
failed_count = 0

for result in test_results:
    if result == "PASSED":
        passed_count += 1
    else:
        failed_count += 1

print("result", passed_count, failed_count)
```

**Output:**
```
result 3 2
```

**Reasoning:**
- Two independent counters, both starting at `0` before the loop — one for each outcome, incremented separately depending on the condition.
- Kept `result` itself untouched throughout the loop — it's just the current item from the list, used only for the comparison, not something to modify.
- Didn't need an extra `elif result == "FAILED":` check — since there are only two possible outcomes, a plain `else` already covers "anything that isn't PASSED".
- This is the same pattern I'd use to summarize a real test run — pass/fail counts calculated the same way, just from real test results instead of a hardcoded list.

## Task 5 — `while` loop for retry logic

**Goal:** simulate a retry loop (a common pattern in automation — retrying a request until a max attempt count is reached).

```python
attempt = 1
max_attempts = 5

while attempt <= max_attempts:
    print(f"attempt: {attempt}")
    attempt += 1
```

**Output:**
```
attempt: 1
attempt: 2
attempt: 3
attempt: 4
attempt: 5
```

**Reasoning:**
- Unlike `for`, `while` doesn't loop over a ready-made collection — it just keeps going as long as the condition stays true, so I have to update the counter myself inside the loop, or it would run forever.
- Getting the comparison direction right mattered: `attempt <= max_attempts` means "keep going while we haven't hit the limit yet" — writing it backwards (`>=`) would've skipped the loop entirely.
- Print happens before the increment, so the first printed value is `1`, not `2` — order of operations inside the loop body matters here.