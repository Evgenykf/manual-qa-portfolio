# Pytest - assert basics

## Task 1 - Testing a simple boolean function

```python
def is_even(number):
    if number % 2 == 0:
        return True
    else:
        return False

def test_is_even():
    assert is_even(20) == True

def test_is_odd():
    assert is_even(11) == False
```

**Output:**
```
collected 2 items
2 passed
```

**Reasoning:** pytest finds any function starting with `test_` inside a file named `test_*.py` and runs it automatically - no manual calls, no `print`. Each `assert` is a claim: if it's true, the test silently passes; if false, pytest reports a `FAILED` with the actual vs expected values. Wrote one test for an even input and one for an odd input to cover both branches of the function.

---

## Task 2 - Testing a normal case and a deliberately failing case

```python
def divide(a, b):
    return a / b

def test_divide():
    assert divide(10, 2) == 5

def test_divide_fail():
    assert divide(10, 2) == 999
```

**Output:**
```
test_divide PASSED
test_divide_fail FAILED
assert 5.0 == 999
 +  where 5.0 = divide(10, 2)
1 failed, 1 passed
```

**Reasoning:** `test_divide_fail` is intentionally wrong to see what a failure looks like - pytest shows exactly what the function actually returned (`5.0`) next to what the assert expected (`999`), which is how you'd debug a real failing test without adding any `print` yourself.

---

## Task 3 - Testing an existing portfolio function with edge cases

```python
def calculate_pass_rate(passed, total):
    percent = passed / total * 100
    percent_rounded = round(percent, 2)
    return percent_rounded

def test_calculate_pass_rate():
    assert calculate_pass_rate(143, 150) == 95.33

def test_calculate_pass_rate_zero():
    assert calculate_pass_rate(0, 150) == 0.0

def test_calculate_pass_rate_full():
    assert calculate_pass_rate(150, 150) == 100.0
```

**Output:**
```
3 passed
```

**Reasoning:** picked three cases on purpose instead of just one - a normal in-between value, the 0% edge case (nothing passed), and the 100% edge case (everything passed). Covering edges like this is exactly the kind of thing manual testing already trained me to think about - just expressed as code instead of a manual test case.

---

## Full final code

```python
def is_even(number):
    if number % 2 == 0:
        return True
    else:
        return False

def divide(a, b):
    return a / b

def calculate_pass_rate(passed, total):
    percent = passed / total * 100
    percent_rounded = round(percent, 2)
    return percent_rounded


def test_is_even():
    assert is_even(20) == True

def test_is_odd():
    assert is_even(11) == False

def test_divide():
    assert divide(10, 2) == 5

def test_divide_fail():
    assert divide(10, 2) == 999

def test_calculate_pass_rate():
    assert calculate_pass_rate(143, 150) == 95.33

def test_calculate_pass_rate_zero():
    assert calculate_pass_rate(0, 150) == 0.0

def test_calculate_pass_rate_full():
    assert calculate_pass_rate(150, 150) == 100.0
```

**Output:**
```
test_basics.py::test_is_even PASSED
test_basics.py::test_is_odd PASSED
test_basics.py::test_divide PASSED
test_basics.py::test_divide_fail FAILED
test_basics.py::test_calculate_pass_rate PASSED
test_basics.py::test_calculate_pass_rate_zero PASSED
test_basics.py::test_calculate_pass_rate_full PASSED

FAILED test_basics.py::test_divide_fail - assert 5.0 == 999
1 failed, 6 passed
```

(the one failure is expected - `test_divide_fail` was written on purpose to fail, to see pytest's failure output)