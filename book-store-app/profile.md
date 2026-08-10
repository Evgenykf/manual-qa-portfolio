# Book Store Application — Profile — Manual Test Cases & Bug Reports
**Module:** Book Store Application → Profile (XQA.io)
**Precondition:** Site is launched, the Profile page is open, user is not logged in
## Test Cases
| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | "Not logged in" state message | 1. Open the Profile page without logging in. | The page should display a message indicating the user is not logged in, along with Login/Register options | Displayed "You are currently not logged in." with "Login" and "Register" links, as expected | Pass | — |
| 2 | "Login" link navigation | 1. Click the "Login" link on the Profile page. | Should redirect to the Login page | Correctly redirected to the Login page | Pass | — |
| 3 | "Register" link navigation | 1. Click the "Register" link on the Profile page. | Should redirect to a registration flow (or equivalent — e.g. a page/message related to creating a new account) | Redirected to the Login page — the same destination as the "Login" link, not a distinct registration page or message | Fail | — |
| 4 | Keyboard navigation via Tab | 1. Click into the page. 2. Press Tab to move through the "Login" and "Register" links. 3. Press Enter to activate a focused link. | Both links should be reachable via Tab and activatable via Enter | Both links were reachable via Tab and could be activated with Enter, in logical order | Pass | — |
## Bug Reports
### Bug #1: "Register" link on Profile page redirects to Login instead of a registration flow
- **Severity:** Low
- **Module:** Book Store Application → Profile
- **Related Test Case:** #3
- **Precondition:** Site is launched, the Profile page is open, user is not logged in
- **Expected Result:** Clicking "Register" should lead somewhere distinct from "Login" — either a registration form, or at minimum a message specific to registration (e.g. the "Registration functionality is not implemented yet" message already used elsewhere in the app for New User)
- **Actual Result:** Clicking "Register" redirects to the same Login page as the "Login" link, with no distinction and no registration-specific messaging shown at the point of redirect
- **Steps to Reproduce:**
  1. Open the Profile page while not logged in.
  2. Click the "Register" link.
  3. Observe that the destination is the Login page, identical to clicking "Login".
- **Status:** New