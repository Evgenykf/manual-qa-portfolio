# Dynamic Properties — Manual Test Cases & Bug Reports

**Module:** Dynamic Properties (XQA.io)
**Precondition:** Site is launched, the Dynamic Properties component is open

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail |
|---|-----------|--------------|------------------|----------------|-----------|
| 1 | Click after enable (3.5s) | 1. Open the website 2. Locate the "Will enable after 3.5 seconds" button 3. Wait until the button label changes from "Disabled" to "Enabled" 4. Click the button | Button responds to click after becoming "Enabled" | Button does not respond | **Fail** |
| 2 | Click after color change (5s) | 1. Open the website 2. Locate the "Will change color after 5 seconds" button 3. Wait until it turns red 4. Click the button | Button responds to click after turning red | Button does not respond | **Fail** |
| 3 | Click after becoming visible (5s) | 1. Open the website 2. Locate the "Will appear after 5 seconds" element 3. Wait until it becomes visible (green) 4. Click the button | Green "Visible" button responds to click | Button does not respond | **Fail** |
| 4 | Timer accuracy check | 1. Open the website 2. Start a stopwatch at the same moment the page loads 3. Observe and record the exact time when each element's state changes | All timers complete at the stated duration (3.5s / 5s / 5s) | Timing confirmed | Pass |
| 5 | Click before state change | 1. Open the website 2. Immediately (before any timers complete) attempt to click each of the three elements | Elements should not be clickable/functional before the timer completes | Elements are not clickable before timer completes, as expected | Pass |
| 6 | Timer reset on page reload | 1. Open the website 2. Wait 2 seconds (before any timer completes) 3. Reload the page (F5) 4. Observe whether the timers restart from the beginning or continue from where they left off | Reloading the page should reset all timers | Confirmed — timers reset on reload | Pass |
| 7 | Independent timers check | 1. Open the website 2. Observe all three elements simultaneously as time passes 3. Confirm that each element changes state independently of the others | All three timers run independently without affecting each other | Confirmed — timers are independent | Pass |

## Bug Reports

### Bug #1: Dynamic elements change visual state but remain non-functional (not clickable)
- **Severity:** High
- **Module:** Dynamic Properties
- **Related Test Cases:** #1, #2, #3
- **Precondition:** Site is launched, the Dynamic Properties component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Wait until each element's timer completes (3.5s / 5s / 5s)
  3. Once the visual state changes (Disabled→Enabled, Color Change→red, element becomes Visible), attempt to click each element
- **Expected Result:** After the timer completes and the element's visual state changes, clicking it should trigger a response (e.g. a confirmation message or state change)
- **Actual Result:** All three elements change their visual state correctly after the timer, but none of them respond to a click — no action, no message, nothing happens
- **Status:** New

**Note:** This is the third component (after Radio Button and Buttons) where interaction appears broken while the underlying timer/animation logic itself works correctly — timers are accurate (#4), reset properly on reload (#6), and run independently (#7). This suggests the issue may specifically be in how the app wires up click handlers to elements, not in the state-management logic itself.