# REST API Playground — Manual Test Cases & Bug Reports

**Module:** REST API Playground (XQA.io)
**Precondition:** Site is launched, the REST API Playground component is open

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Valid GET request (list) | 1. Select GET. 2. Enter "/api/users". 3. Click Send. 4. Observe status and body. | Returns 200 OK with an array of user objects (id, name, email) | Returned 200 OK, array of 2 users with id/name/email fields, as expected | Pass | GET /api/users |
| 2 | Valid POST request (create user) | 1. Select POST. 2. Enter "/api/users". 3. Enter body {"name":"John Doe","email":"john@example.com"}. 4. Click Send. | Returns 201 Created with the new user object including a new id | Returned 201 Created with id:3, name, email, created:true, as expected | Pass | {"name":"John Doe","email":"john@example.com"} |
| 3 | Check whether created user persists | 1. Perform POST /api/users. 2. Perform GET /api/users. 3. Check whether the new user appears in the list. | The newly created user should appear in the subsequent GET /api/users list | The new user (id:3) did NOT appear in the list — only the original 2 users were returned | Fail | Same as above |
| 4 | Check id increment consistency across repeated POSTs | 1. Perform POST /api/users twice in a row with the same body. 2. Compare the returned id values. | Each POST should return a new, incrementing id (e.g., 3, then 4) | Both POST requests returned the same id:3 — no real increment/persistence | Fail | {"name":"John Doe","email":"john@example.com"} |
| 5 | Valid GET request (single user) | 1. Select GET. 2. Enter "/api/users/1". 3. Click Send. | Returns 200 OK with the specific user's data | Returned 200 OK with id:1, name, email, and an extra "role":"admin" field | Pass | GET /api/users/1 |
| 6 | Check schema consistency between list and single-user endpoints | 1. Perform GET /api/users. 2. Perform GET /api/users/1. 3. Compare the fields returned for the same user. | The same user should have identical fields in both the list and single-user responses | GET /api/users/1 returned an extra "role" field not present in the GET /api/users list response | Fail | — |
| 7 | Repeated identical GET requests consistency check | 1. Send GET /api/users/1 five times in a row with no other requests in between. 2. Observe each response. | All five requests should return the same result (200 OK) | All 5 consecutive requests returned 200 OK consistently | Pass | GET /api/users/1 |
| 8 | Verify 404 error handling | 1. Select GET. 2. Enter "/api/404". 3. Click Send. | Returns 404 Not Found with a meaningful error message | Returned 404, body: {"error":"Not Found","message":"Resource not found"}, as expected | Pass | GET /api/404 |
| 9 | Verify 500 error handling | 1. Select GET. 2. Enter "/api/error". 3. Click Send. | Returns 500 Internal Server Error with a meaningful error message | Returned 500, body: {"error":"Internal Server Error","message":"Something went wrong"}, as expected | Pass | GET /api/error |
| 10 | DELETE without specifying an ID | 1. Select DELETE. 2. Enter "/api/users" (no ID). 3. Click Send. | Should return an error (e.g., 404), since no specific resource is targeted | Returned 404 "Endpoint not found", as expected | Pass | DELETE /api/users |
| 11 | Valid DELETE request | 1. Select DELETE. 2. Enter "/api/users/1". 3. Click Send. | Returns 204 No Content, indicating successful deletion | Returned 204 No Content, body: null, as expected | Pass | DELETE /api/users/1 |
| 12 | Repeated DELETE on the same (already-deleted) resource | 1. Perform DELETE /api/users/1. 2. Perform DELETE /api/users/1 again immediately after. 3. Compare the two responses. | The second DELETE should return 404 (resource no longer exists) | The second DELETE also returned 204, identical to the first — no real state tracking | Fail | DELETE /api/users/1 |
| 13 | Response time check across endpoints | 1. Record the response time shown for each request sent during testing. | All responses should complete within a reasonable threshold (e.g., under ~2s) | All observed response times ranged between ~520ms and ~980ms — within a reasonable threshold | Pass | — |
| 14 | Valid PUT request (update user) | 1. Select PUT. 2. Enter "/api/users/1". 3. Enter body {"name":"John Updated","email":"john.updated@example.com"}. 4. Click Send. | Returns 200 OK, with the response reflecting the submitted data | Returned 200 OK, but the response body contained hardcoded data ("Updated Name" / "updated@example.com") unrelated to the submitted request body | Fail | {"name":"John Updated","email":"john.updated@example.com"} |
| 15 | Check whether the update persists | 1. Perform PUT /api/users/1 with updated data. 2. Perform GET /api/users/1. 3. Compare the returned data to the update sent. | The updated data should be reflected in the subsequent GET request | GET /api/users/1 returned the original, unchanged data ("John Doe" / "john@example.com") — the update was not persisted | Fail | Same as above |

## Bug Reports

### Bug #1: API does not persist state between requests (mock/stateless behavior)
- **Severity:** Medium
- **Module:** REST API Playground
- **Related Test Cases:** #3, #4, #12, #14, #15
- **Precondition:** Site is launched, the REST API Playground component is open
- **Expected Result:** Created/updated/deleted resources should be reflected in subsequent requests (real persistence), and repeated/related operations should behave according to the resource's actual current state
- **Actual Result:** Neither POST, PUT, nor DELETE affect the data returned by subsequent requests. PUT additionally returns a hardcoded response unrelated to the submitted request body, rather than reflecting the actual input
- **Steps to Reproduce:**
  1. Perform POST /api/users and note the returned id; perform GET /api/users — the new user is missing.
  2. Perform POST /api/users again — the same id is returned instead of an incremented one.
  3. Perform DELETE /api/users/1 twice in a row — both return 204 instead of the second returning 404.
  4. Perform PUT /api/users/1 with custom data ("John Updated" / "john.updated@example.com") — the response returns different, hardcoded data ("Updated Name" / "updated@example.com") instead of echoing the submitted values; a subsequent GET /api/users/1 shows the original unchanged data.
- **Status:** New

### Bug #2: GET /api/users/1 and GET /api/users return inconsistent field sets for the same user
- **Severity:** Low
- **Module:** REST API Playground (Users)
- **Related Test Case:** #6
- **Precondition:** Site is launched, the REST API Playground component is open
- **Expected Result:** The same user should return the same schema/fields (id, name, email) in both endpoints
- **Actual Result:** GET /api/users/1 includes an additional "role":"admin" field not present in the GET /api/users list response
- **Steps to Reproduce:**
  1. Perform GET /api/users and inspect user id:1's fields.
  2. Perform GET /api/users/1 and inspect the same user's fields.
  3. Compare the two.
- **Status:** New
