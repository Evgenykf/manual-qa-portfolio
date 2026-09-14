# Python — Functions (def, lambda)

Practice exercises on defining functions, return values, default arguments, and lambda — with a QA-oriented framing for each task.

## Task 1 — Basic function with a return value

**Goal:** wrap a percentage calculation in a reusable function instead of computing it inline.

```python
def calculate_percent(success, total):
    percent = success / total * 100
    percent_rounded = round(percent, 2)
    return percent_rounded

result = calculate_percent(143, 150)
print(result)
```

**Output:**
```
95.33
```

**Reasoning:**
- Same formula used earlier in the basics topic (`success / total * 100`, rounded), but now wrapped as a function — can call it with any numbers instead of rewriting the calculation each time.
- `return` sends the value back out of the function so it can be stored or printed; without it, the function would just calculate the value and lose it.

## Task 2 — Function with conditional branching

**Goal:** combine `if`/`elif`/`else` with a function — return a status string instead of printing it directly.

```python
def check_status(code):
    if code == 200:
        return "OK"
    elif code == 404:
        return "Not Found"
    else:
        return "Unknown"

print(check_status(200))
print(check_status(404))
print(check_status(500))
```

**Output:**
```
OK
Not Found
Unknown
```

**Reasoning:**
- As soon as one `return` executes, the function stops immediately — no need to worry about "falling through" to the other branches afterward, unlike `print()` inside a loop which just keeps going.

## Task 3 — Default argument value

**Goal:** give a parameter a default value so it's optional when calling the function.

```python
def run_test(name, retries=3):
    print(f"Running {name} with {retries} retries")

run_test("login_test", 5)
run_test("logout_test")
```

**Output:**
```
Running login_test with 5 retries
Running logout_test with 3 retries
```

**Reasoning:**
- `retries=3` in the function definition means the caller doesn't have to pass it — if they do (like the first call, `5`), that value overrides the default; if they don't, Python falls back to `3`.
- Kept mixing this up at first with wrapping the calls in `print()` — but this function already prints internally and doesn't `return` anything, so wrapping it in `print()` would just print `None` after the real output.

## Task 4 — Function that works on a list

**Goal:** combine a function with a list method (`.count()`) covered in an earlier topic.

```python
def count_passed(results):
    return results.count("PASSED")

text = ["PASSED", "FAILED", "PASSED"]
print(count_passed(text))
```

**Output:**
```
2
```

**Reasoning:**
- Ran into scope confusion here — first tried defining the list (`text`) *inside* the function, but a variable created inside a function isn't visible outside it. The list needs to exist before it's passed in as an argument.
- Also had a line calculating `.count()` without `return` — same "calculated but never saved" mistake as division results earlier in the course; the value just gets lost without `return`.

## Task 5 — Sorting with `lambda`

**Goal:** sort a list of tuples by a specific element (execution time) using `sorted()` with `key=lambda`.

```python
tests = [("test_a", 1.2), ("test_b", 0.3), ("test_c", 2.5)]

sorted_tests = sorted(tests, key=lambda t: t[1])
print(sorted_tests)
```

**Output:**
```
[('test_b', 0.3), ('test_a', 1.2), ('test_c', 2.5)]
```

**Reasoning:**
- `key=lambda t: t[1]` tells `sorted()` what to compare instead of sorting by the default (first element of each tuple, which would've sorted by name instead of time).
- The same name (`t` here) has to appear on both sides of the `lambda` — once to represent "the current tuple," once to index into it (`t[1]`) — using two different names there doesn't work, since the right side needs to reference the thing defined on the left.
- `sorted()` doesn't modify the original list — it returns a new one, which has to be caught in a variable (or printed directly) or the result is lost, same as with `.strip()`/`.lower()` earlier — string/list-returning operations don't change things in place unless the method specifically says so (like `.sort()` or `.append()` did).
- This wasn't covered in the course itself — had to search for the `sorted()` + `key=lambda` pattern, since it's a very common way to sort by a custom rule in real code (e.g. sorting test results by execution time or by status).