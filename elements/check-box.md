# Check Box — Manual Test Cases & Bug Reports

**Module:** Check Box (XQA.io)
**Precondition:** Site is launched, the Check Box component is open

## Test Cases

| # | Test Description | Expected Result | Actual Result | Pass/Fail |
|---|-------------------|------------------|----------------|-----------|
| 1 | Select a parent node (e.g. "Home") | All child elements become selected automatically | All child elements became selected | Pass |
| 2 | Deselect a parent node with selected children (e.g. "Desktop") | All child elements should be deselected automatically | Child elements (Notes, Commands) remained selected | **Fail** |
| 3 | Repeat deselection on a different branch (Documents/WorkSpace/Office) | Same automatic deselection behavior expected | Same desync issue reproduced | **Fail** |
| 4 | Repeat deselection on another branch (Home/Documents) | Same automatic deselection behavior expected | Same desync issue reproduced | **Fail** |

## Bug Reports

### Bug #1: No synchronization between parent and child checkbox states
- **Severity:** High
- **Precondition:** Site is launched, the Check Box component is open
- **Steps to Reproduce:**
  1. Open the website
  2. Expand the "Desktop" node under "Home"
  3. Verify "Notes" and "Commands" (child checkboxes) are selected
  4. Uncheck the parent checkbox "Desktop"
  5. Observe the state of "Notes" and "Commands"
- **Expected Result:** All child elements of Desktop (Notes, Commands) should be removed/deselected automatically
- **Actual Result:** Desktop became unselected, but Notes and Commands remained selected
- **Additional Playback Examples:** The same behavior is reproducible on Documents/WorkSpace/Office and on Home/Documents branches — confirms this is a systemic issue with the component, not an isolated case
- **Status:** New