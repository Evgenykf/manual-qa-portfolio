# Web Tables — Manual Test Cases & Bug Reports

**Module:** Web Tables (XQA.io)
**Precondition:** Site is launched, the Web Tables component is open

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Add a new data field | 1. Open the website 2. Find the "Add" button 3. Click it 4. Enter the correct information | Adding a new table | The button is not illuminated and cannot be pressed | **Fail** | — |
| 2 | Checking for disabled buttons | 1. Open the website 2. Find the "Previous" and "Next" buttons 3. Click on them | It is not selected or pressed | The button is not illuminated and cannot be pressed | Pass | — |
| 3 | Checking for incorrect data entry | 1. Open the website 2. Find the "Search query" button 3. Click on it 4. Enter incorrect data from the corresponding column | No data or errors are displayed | No data or errors are displayed | Pass | AHFden.VEga. 455 . aA!den@@example.com .20200 .legal |
| 4 | Data entry verification | 1. Open the website 2. Find the "Search Input" button 3. Click on it 4. Enter the data from the column | As you enter data, a column with the entered data is displayed | As you enter data, the required table is displayed | Pass | Alden.Vega. 45 . alden@example.com .2000 .Legal |
| 5 | Check page changes | 1. Open the website 2. Find the "Page" button 3. Click it 4. Enter the next page | The page switches to the next one | Unable to navigate to another page | **Fail** | 2 |
| 6 | Checking for data changes | 1. Open the website 2. Find the "Edit" button 3. Click it 4. Change the data in the column | By clicking on the button you can change the data | The button is not illuminated and cannot be pressed | **Fail** | — |
| 7 | Data deletion check | 1. Open the website 2. Find the "Delete" button 3. Click it 4. Delete the data in the column | By clicking the button, you can delete the data | The table is deleted | Pass | Cierra Vega 39 cierra@example.com 10000 Insurance |

## Bug Reports

### Bug #1: "Add" and "Edit" buttons don't open a form (likely same root cause)
- **Severity:** Critical
- **Module:** Web Tables
- **Related Test Cases:** #1, #6
- **Precondition:** Site is launched, the Web Tables component is open
- **Expected Result:** Clicking "Add" should open a form to add a new record; clicking the edit (pencil) icon should open a form pre-filled with the selected row's data
- **Actual Result:** Clicking either "Add" or the edit icon produces no visible reaction — no form, no modal, no error. Both actions share the same symptom, suggesting a shared issue with modal rendering
- **Status:** New

### Bug #2: "Page" field appears editable but value cannot be changed
- **Severity:** Medium
- **Module:** Web Tables
- **Related Test Case:** #5
- **Precondition:** Site is launched, the Web Tables component is open
- **Expected Result:** The user should be able to clear/change the value in the "Page" field to navigate to a different page (or the field should not appear as an editable input if it isn't intended to be)
- **Actual Result:** The value "1" in the "Page" field cannot be deleted or changed
- **Status:** New

**Note:** Test data used for invalid input checks (e.g. "AHFden.VEga. 455 . aA!den@@example.com .20200 .legal") was intentionally generated with malformed characters (double "@@", inconsistent formatting) to test field validation boundaries.