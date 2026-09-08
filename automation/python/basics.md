# Python — Basics: Variables & Data Types

Practice exercises on core Python fundamentals: variables, data types, type conversion, f-strings, and basic arithmetic — with a QA-oriented framing for each task.

## Task 1 — Variable output via f-string

**Goal:** store test-case data in variables of the correct type (`int`, `str`, `bool`, `float`) and print it in a readable format using an f-string.

```python
test_id = 1
test_name = "Login with valid credentials"
is_passed = True
execution_time = 0.5

status = "PASSED" if is_passed else "FAILED"
print(f'Test #{test_id} "{test_name}" - {status} ({execution_time}s)')
```

**Output:**
```
Test #1 "Login with valid credentials" - PASSED (0.5s)
```

**Reasoning:**
- I kept each variable in its natural type instead of turning them into text early — formatting only happens in the final `print()`, not while creating the data.
- `status` is a separate variable instead of overwriting `is_passed`, so I still have the original `True`/`False` if I need to check it again later.
- Used an f-string to build the final message — same tool I'll keep using for test logs and error messages.

## Task 2 — Checking variable types

**Goal:** confirm the actual type of each variable using `type()`, and understand why `bool` is used for pass/fail status instead of `str` or `int`.

```python
test_id = 1
test_name = "Login with valid credentials"
is_passed = True
execution_time = 0.5

print(type(test_id), type(test_name), type(is_passed), type(execution_time))
```

**Output:**
```
<class 'int'> <class 'str'> <class 'bool'> <class 'float'>
```

**Reasoning:**
- `bool` only has two possible values, so there's no room for typos or inconsistent formatting — unlike a string like `"PASSED"`, which could be typed as `"Passed"` or `"passed "` by mistake.
- If pass/fail were stored as a string instead, a check that only tests "is this value set" would treat `"FAILED"` as truthy too, since any non-empty string is truthy in Python.
- This matters for real API responses — a field like `status_code` needs to actually be the right type, not just look right, for comparisons to work.

## Task 3 — Type conversion (str → int)

**Goal:** demonstrate a common bug source in API testing — comparing a string value to a number without converting it first.

```python
status_code = "200"

print(status_code == 200)

status_code_int = int(status_code)
print(status_code_int == 200)
```

**Output:**
```
False
True
```

**Reasoning:**
- `"200"` and `200` are different types, so `==` returns `False` even though the digits match — Python checks type, not just the value.
- `int()` converts the string to a real number so the comparison works as expected.
- This is a real trap in API testing: fields that look numeric sometimes come back as strings in a JSON response, and skipping the conversion makes a correct-looking test silently fail.

## Task 4 — `input()` and type conversion

**Goal:** practice the pattern of getting text input and converting it to a usable type before working with it.

```python
name = input("What is your name? ")
print(f"Привет, {name}")

age = input("Enter your age: ")
age = int(age)
print(f"Ваш возраст: {age}")
```

**Reasoning:**
- `input()` always returns a string, even for digits — `int()` is needed before the value can be used in any calculation.
- I won't use `input()` directly in automated tests, but it's the same idea as Task 3: data that looks like a number still needs its type checked before use.

## Task 5 — Arithmetic and rounding

**Goal:** calculate a percentage from raw counts and round the result for readable output — a pattern applicable to test-run summaries (e.g. pass rate, request success rate).

```python
requests_sent = 150
requests_failed = 7

requests_success = requests_sent - requests_failed
success_percent = requests_success / requests_sent * 100
success_percent_rounded = round(success_percent, 2)

print(f"Успешных запросов: {success_percent_rounded}%")
```

**Output:**
```
Успешных запросов: 95.33%
```

**Reasoning:**
- I split the calculation into separate variables (`requests_success`, `success_percent`, `success_percent_rounded`) instead of reusing one — makes it easy to see where a wrong number would be coming from.
- Dividing two `int` values always gives a `float` in Python, even for a whole number — that's why the raw percentage comes out as a long decimal before rounding.
- Edge case worth noting: if `requests_sent` were `0`, this would throw a `ZeroDivisionError`. Worth remembering as a boundary case — real test data shouldn't assume there's always at least one request.