# Book Store Application — Login — Manual Test Cases & Bug Reports
**Module:** Book Store Application → Login (XQA.io)
**Precondition:** Site is launched, the Book Store Application — Login page is open
## Test Cases
| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Login attempt with no known valid credentials | 1. Enter username "Vlad". 2. Enter each password variation in turn. 3. Click Login for each combination. | No valid credentials exist on this demo instance (registration is disabled, so no accounts can be created); system should return "Invalid username or password" for all combinations | All combinations returned "Invalid username or password!" as expected | Pass | us: Vlad; ps: 1253 / 123! / 125go! / 2dfD |
| 2 | Login with special characters / Cyrillic input (charset validation) | 1. Enter username "Vladикд3". 2. Enter password "авда/-". 3. Click Login. | System should reject the input gracefully (validation error or "Invalid username or password"), without crashing or breaking the UI | Returned "Invalid username or password!" — handled gracefully, no crash or visual break | Pass | us: Vladикд3; ps: авда/- |
| 3 | "New User" button behavior | 1. Click the "New User" button. | Clicking should either open a registration form, or — if registration is unavailable — display a clear message explaining why | Displayed message: "Registration functionality is not implemented yet." | Pass | — |
| 4 | Keyboard navigation via Tab | 1. Click into the UserName field. 2. Press Tab repeatedly to move through Password, Login, and New User. | All interactive elements (UserName, Password, Login, New User) should be reachable and focusable via Tab, in logical order | All four elements were reachable via Tab in the expected order | Pass | — |
| 5 | Login with empty fields | 1. Leave UserName and Password empty. 2. Attempt to click Login. | System should prevent submission with empty required fields (either by disabling the button or showing a validation message) | The Login button is disabled and cannot be clicked while UserName and/or Password are empty — submission is blocked client-side | Pass | UserName: empty; Password: empty |
## Bug Reports
No bugs identified in this module.
**Note:** Initial testing suggested two potential issues — the Login button appearing unresponsive with both valid-looking and invalid credentials, and the New User button appearing unresponsive. Further investigation showed both were expected behavior, not defects:
- Registration is explicitly disabled on this demo instance ("Registration functionality is not implemented yet"), so no valid user accounts can exist.
- Since no valid accounts exist, "Invalid username or password" on every login attempt is the correct, expected response — not a sign of a broken Login button.
This is documented here rather than as a bug, since the actual behavior matches what the system is designed to do, confirmed by the on-screen message and the absence of any known working credentials for this specific module.