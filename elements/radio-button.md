# Radio Button — Manual Test Cases & Bug Reports

**Module:** Radio Button (XQA.io)
**Precondition:** Site is launched, the Radio Button component is open

## Bug Reports

### Bug #1: Option "No" cannot be selected
- **Severity:** Medium
- **Module:** Radio Button
- **Precondition:** Site is launched, the Radio Button component is open
- **Expected Result:** The user should be able to select any of the three answer options, including "No"
- **Actual Result:** Clicking on the "No" option has no effect, the selection is not applied; visually the element appears dimmed/disabled
- **Status:** New

### Bug #2: Radio buttons are not accessible via keyboard navigation (Tab)
- **Severity:** High
- **Module:** Radio Button
- **Precondition:** Site is launched, the Radio Button component is open
- **Expected Result:** When pressing Tab sequentially, focus should reach each radio button (Yes, Impressive, No) with a visible focus indicator
- **Actual Result:** Tab completely skips the radio button group — focus is never set on them, while all other elements on the page receive focus normally
- **Status:** New

**Note:** Bug #1 and Bug #2 may share a common root cause — if the "No" option is excluded from the page's interactive/focusable elements entirely, this could explain both its unresponsiveness to clicks and its absence from Tab navigation. Worth investigating together.