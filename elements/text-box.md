# Text Box — Manual Test Cases & Bug Reports

**Module:** Text Box (XQA.io)
**Precondition:** Site is launched, the Text Box component is open

## Test Cases

| # | Field | Test Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-------|-------------------|------------------|----------------|-----------|-----------|
| 1 | Full Name | Enter valid name | Success | Success | Pass | Roma |
| 2 | Full Name | Enter invalid name (digits/symbols) | Error expected | Success (no validation) | **Fail** | 123@! |
| 3 | Full Name | Leave field empty | Error expected | Success (no validation) | **Fail** | — |
| 4 | Email | Enter valid email | Success | Success | Pass | x7k9p@mail.ru |
| 5 | Email | Enter invalid email | Error expected | Error | Pass | тест@почта.рф |
| 6 | Email | Leave field empty | Error expected | Success (no validation) | **Fail** | — |

## Bug Reports

### Bug #1: Full Name field accepts invalid characters and empty input
- **Severity:** Medium
- **Precondition:** Site is launched, the Text Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Find the "Full Name" field
  3. Enter digits/symbols (e.g. "123@!") or leave the field empty
  4. Click Submit
- **Expected Result:** Field should reject non-letter characters and require input
- **Actual Result:** Form accepts any input, including empty field
- **Status:** New

### Bug #2: Empty Email field is accepted
- **Severity:** Medium
- **Precondition:** Site is launched, the Text Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Find the "Email" field
  3. Leave it empty
  4. Click Submit
- **Expected Result:** Form should show validation error
- **Actual Result:** Form accepts submission without email
- **Status:** New