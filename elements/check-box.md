# Check Box — Manual Test Cases & Bug Reports

**Module:** Check Box (XQA.io)
**Precondition:** Site is launched, the Check Box component is open

## Bug Reports

### Bug #1: No synchronization between the state of the parent and child checkboxes
- **Severity:** High
- **Module:** Check Box
- **Precondition:** The site is launched, the Check Box component is open
- **Expected Result:** All child elements of Desktop (Notes, Commands) should be removed automatically
- **Actual Result:** Desktop became unselected, but Notes and Commands remained selected
- **Additional Playback Examples:** The same behavior is visible on Documents/WorkSpace/Office, and on Home/Documents
- **Status:** New