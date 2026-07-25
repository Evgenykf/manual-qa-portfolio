# Buttons — Manual Test Cases & Bug Reports

**Module:** Buttons (XQA.io)
**Precondition:** Site is launched, the Buttons component is open

## Test Cases

| # | Element | Test Name | Description | Expected Result | Actual Result | Pass/Fail |
|---|---------|-----------|--------------|------------------|----------------|-----------|
| 1 | Double Click Me | Double click verification | 1. Open the website 2. Find "Double Click Me" button 3. Double-click it | The message "You have done a double click" appears in green | The message "You have done a double click" appeared in green, as expected | Pass |
| 2 | Double Click Me | Double click with delay | 1. Open the website 2. Find "Double Click Me" button 3. Click once, wait 2-3 seconds, click again | The message "You have done a double click" should NOT appear | As expected, the message "You have done a double click" did not appear | Pass |
| 3 | Double Click Me | Double right-click | 1. Open the website 2. Find "Double Click Me" button 3. Double-click using the right mouse button | The message "You have done a double click" should NOT appear | As expected, the message "You have done a double click" did not appear | Pass |
| 4 | Right Click Me | Right click verification | 1. Open the website 2. Find the "Right-click me" button 3. Click it | The message "You have done a right click" appears in green | As expected, the message "You have done a right click" did appear | Pass |
| 5 | Right Click Me | Left-click on the "Right Click Me" button | 1. Open the website 2. Find the "Right-click on me" button 3. Right-click on "Click on me" | The message "You have done a right click" should NOT appear | As expected, the message "You have done a right click" did not appear | Pass |
| 6 | Click Me | Right click on "Click Me" | 1. Open the website 2. Find the "Click me" button 3. Right-click on "Click me" | The message "You have done a dynamic click" should NOT appear | As expected, the message "You have done a dynamic click" did not appear | Pass |
| 7 | Click Me | Left click verification | 1. Open the website 2. Find the "Click me" button 3. Left-click on "Click me" | The message "You have done a dynamic click" appears in green | As expected, the message "You have done a dynamic click" did appear | Pass |
| 8 | Keyboard navigation | Keyboard navigation | 1. Open the website 2. Select the button using the Tab key 3. Click the button | Focus visibly moves to the button when pressing Tab; pressing Enter/Space triggers the corresponding message | It is not possible to select a button using Tab | **Fail** |

## Bug Reports

### Bug #1: Buttons are not accessible via keyboard navigation (Tab)
- **Severity:** High
- **Module:** Buttons
- **Related Test Case:** #8
- **Precondition:** Site is launched, the Buttons component is open
- **Expected Result:** When pressing Tab sequentially, focus should reach each button (Double Click Me, Right Click Me, Click Me) with a visible focus indicator
- **Actual Result:** Tab does not move focus to any of the buttons — it is not possible to select a button using Tab
- **Status:** New

**Note:** This is the second component (after Radio Button) where Tab-based keyboard navigation is completely broken — suggests a possible site-wide accessibility issue rather than an isolated bug per component.