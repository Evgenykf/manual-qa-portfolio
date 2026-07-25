# Text Box — Manual Test Cases & Bug Reports

**Module:** Text Box (XQA.io)
**Precondition:** Site is launched, the Text Box component is open

## Test Cases

| # | Field | Test Name | Description | Input Condition | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-------|-----------|--------------|------------------|------------------|----------------|-----------|-----------|
| 1 | Full Name | Enter Name | 1. Open site 2. Find Full Name field 3. Enter value 4. Click Submit | Enter valid Name | Success | Success | Pass | Roma |
| 2 | Full Name | Enter Name | 1. Open site 2. Find Full Name field 3. Enter value 4. Click Submit | Enter invalid Name (digits and symbols) | Error | Success | **Fail** | 123@! |
| 3 | Full Name | Enter Name | 1. Open site 2. Find Full Name field 3. Enter value 4. Click Submit | Leave field empty | Error | Success | **Fail** | — |
| 4 | Email | Enter Email | 1. Open site 2. Find Email field 3. Enter value 4. Click Submit | Enter valid Email | Success | Success | Pass | x7k9p@mail.ru |
| 5 | Email | Enter Email | 1. Open site 2. Find Email field 3. Enter value 4. Click Submit | Enter invalid Email (multiple @ and non-English letters) | Error | Error | Pass | тест@почта.рф |
| 6 | Email | Enter Email | 1. Open site 2. Find Email field 3. Enter value 4. Click Submit | Leave field empty | Error | Success | **Fail** | — |
| 7 | Current Address | Enter Current Address | 1. Open site 2. Find Current Address field 3. Enter value 4. Click Submit | Enter valid Address | Success | Success | Pass | Россия, г. Нижний Новгород, ул. Полтавская, д. 11 |
| 8 | Current Address | Enter Current Address | 1. Open site 2. Find Current Address field 3. Enter value 4. Click Submit | Enter invalid Address (non-existent / random symbols) | Error | Success | **Fail** | 123W@, г. Страый Новгород, ул. Полтавская, д. 11 |
| 9 | Current Address | Enter Current Address | 1. Open site 2. Find Current Address field 3. Enter value 4. Click Submit | Leave field empty | Error | Success | **Fail** | — |
| 10 | Permanent Address | Enter Permanent Address | 1. Open site 2. Find Permanent Address field 3. Enter value 4. Click Submit | Enter valid Address | Success | Success | Pass | Россия, г. Нижний Новгород, ул. Полтавская, д. 11 |
| 11 | Permanent Address | Enter Permanent Address | 1. Open site 2. Find Permanent Address field 3. Enter value 4. Click Submit | Enter invalid Address (non-existent / random symbols) | Error | Success | **Fail** | 123W@, г. Страый Новгород, ул. Полтавская, д. 11 |
| 12 | Permanent Address | Enter Permanent Address | 1. Open site 2. Find Permanent Address field 3. Enter value 4. Click Submit | Leave field empty | Error | Success | **Fail** | — |
| 13 | All fields | Enter all correct data | 1. Open site 2. Find all fields 3. Enter values 4. Click Submit | Enter all correct data | Success | Success | Pass | Name: Roma. Email: x7k9p@mail.ru. Current Address: Россия, г. Нижний Новгород, ул. Полтавская, д. 11. Permanent Address: Россия, г. Нижний Новгород, ул. Полтавская, д. 11 |
| 14 | All fields | Enter all incorrect data | 1. Open site 2. Find all fields 3. Enter values 4. Click Submit | Enter all incorrect data | Error | Error | **Fail** | Name: 123@! Email: тест@почта.рф. Current Address: 123W@, г. Страый Новгород, ул. Полтавская, д. 11. Permanent Address: 123W@, г. Страый Новгород, ул. Полтавская, д. 11 |
| 15 | All fields | Submit empty form | 1. Open site 2. Find all fields 3. Leave all fields empty 4. Click Submit | Enter nothing in any field | Error | Success | **Fail** | — |

## Bug Reports

### Bug #1: Full Name field accepts invalid characters
- **Severity:** Medium
- **Related Test Case:** #2
- **Precondition:** Site is launched, the Text Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Find the "Full Name" field
  3. Enter digits/symbols (e.g. "123@!")
  4. Click Submit
- **Expected Result:** Field should reject non-letter characters
- **Actual Result:** Form accepts the input as valid ("success")
- **Status:** New

### Bug #2: Full Name, Current Address, Permanent Address fields accept empty input
- **Severity:** Medium
- **Related Test Cases:** #3, #9, #12, #15
- **Precondition:** Site is launched, the Text Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Leave "Full Name" / "Current Address" / "Permanent Address" field(s) empty
  3. Click Submit
- **Expected Result:** Form should require these fields and show a validation error
- **Actual Result:** Form accepts submission without required fields filled in
- **Status:** New

### Bug #3: Current Address and Permanent Address fields accept invalid/random values
- **Severity:** Medium
- **Related Test Cases:** #8, #11
- **Precondition:** Site is launched, the Text Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Enter a random/non-existent address (e.g. "123W@, г. Страый Новгород, ул. Полтавская, д. 11") into "Current Address" or "Permanent Address"
  3. Click Submit
- **Expected Result:** Form should validate address format and reject clearly invalid values
- **Actual Result:** Form accepts any input as valid
- **Status:** New

**Note:** Email field is the only field with working validation on this form — it correctly rejects malformed input (Test Case #5) and correctly requires a value (though this contradicts Test Case #6 result, worth re-checking).