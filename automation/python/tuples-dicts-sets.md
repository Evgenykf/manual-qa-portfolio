# Python — Tuples, Dictionaries, Sets

Practice exercises on tuples (immutability), dictionaries, and sets — with a QA-oriented framing for each task.

## Task 1 — Tuples: immutability

**Goal:** access tuple elements by index, then see what happens when trying to modify one.

```python
test_info = ("login_test", "API", "PASSED")

print(test_info[0])
print(test_info[2])

test_info[0] = "new_test"
```

**Output (last line raises an error):**
```
login_test
PASSED
TypeError: 'tuple' object does not support item assignment
```

**Reasoning:**
- Tuples index the same way lists do (`[0]`, `[-1]`, slices), but unlike lists, they can't be changed after creation — no `.append()`, `.remove()`, or item assignment.
- This is useful for data that shouldn't change accidentally partway through a script — e.g. fixed expected values or reference data for a test. If something tries to modify it, Python raises an error immediately instead of silently corrupting the data.

## Task 2 — Dictionaries: creation and access

**Goal:** read values by key and add a new key to a dictionary.

```python
test_case = {"name": "login_test", "type": "API", "status": "PASSED", "duration": 0.5}

print(test_case["name"])
print(test_case["status"])

test_case["retries"] = 0
print(test_case["retries"])
```

**Output:**
```
login_test
PASSED
0
```

**Reasoning:**
- Dictionary keys work like list indexes, but with a name instead of a position — `dict["key"]` instead of `list[0]`.
- Assigning to a key that doesn't exist yet (`test_case["retries"] = 0`) creates it; assigning to an existing key would just overwrite its value.

## Task 3 — Dictionaries: methods

**Goal:** list all keys, all values, and check whether a key exists.

```python
test_case = {"name": "login_test", "type": "API", "status": "PASSED", "duration": 0.5}

print(test_case.keys())
print(test_case.values())
print("status" in test_case)
```

**Output:**
```
dict_keys(['name', 'type', 'status', 'duration'])
dict_values(['login_test', 'API', 'PASSED', 0.5])
True
```

**Reasoning:**
- `in` on a dictionary checks for a matching **key**, not a value — `"login_test" in test_case` would be `False`, since `"login_test"` is a value, not a key. Worth remembering when checking whether an API response contains a specific field vs a specific value.

## Task 4 — Sets: union and intersection

**Goal:** find tags common to two tests, and all tags combined across both.

```python
tags_test1 = {"smoke", "api", "regression"}
tags_test2 = {"api", "critical", "regression"}

tags_all = tags_test1.union(tags_test2)
print(tags_all)

tags_common = tags_test1.intersection(tags_test2)
print(tags_common)
```

**Output:**
```
{'api', 'critical', 'regression', 'smoke'}
{'api', 'regression'}
```

**Reasoning:**
- Regular list/string tools (`+`, `.append()`) don't work on sets — sets have their own methods for combining (`.union()`) and comparing (`.intersection()`).
- Set order isn't guaranteed — printing the same set twice can show elements in a different order, since sets aren't indexed like lists.

## Task 5 — Sets: removing duplicates and counting

**Goal:** get unique values from a list, and count how many times one specific value occurs.

```python
statuses = ["PASSED", "FAILED", "PASSED", "PASSED", "SKIPPED", "FAILED"]

unique_statuses = set(statuses)
print(unique_statuses)

print(statuses.count("PASSED"))
```

**Output:**
```
{'PASSED', 'FAILED', 'SKIPPED'}
3
```

**Reasoning:**
- Converting a list to a `set()` is the simplest way to drop duplicates — sets can't contain repeated values by definition, no loop or condition needed.
- `.count()` is a list method for counting how many times a specific value appears — useful for quick pass/fail tallies without writing a manual loop with a counter (like the counter-based approach used back in the control-flow topic).