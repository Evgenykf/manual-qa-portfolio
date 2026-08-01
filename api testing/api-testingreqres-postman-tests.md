# API Testing — ReqRes (Postman)

**Tool:** Postman
**API:** [ReqRes](https://reqres.in)
**Precondition:** Postman installed, ReqRes Environment configured with `base_url` and `api_key`

## Setup

**Environment: ReqRes**

| Variable | Value |
|----------|-------|
| `base_url` | `https://reqres.in` |
| `api_key` | personal key obtained from app.reqres.in |

All requests require the `x-api-key` header (ReqRes previously allowed unauthenticated access to `/api/users/*`; this changed to require an API key partway through testing — an example of an API's behavior shifting over time, which testers need to stay alert to).

**Collection:** `My Collection`, organized by resource lifecycle order: **POST → GET → PUT → DELETE** (create → read → update → delete)

## Test Cases & Automated Checks

| # | Method | Endpoint | Purpose | Status | Automated Test (pm.test) | Result |
|---|--------|----------|---------|--------|---------------------------|--------|
| 1 | POST | `{{base_url}}/api/users` | Create a user | 201 | None written — verified manually that `id` and `createdAt` were present in the response | Pass (manual check) |
| 2 | GET | `{{base_url}}/api/users/2` | Retrieve a user by id | 200 | `Status code is 200`, `First name is Janet` | Pass |
| 3 | PUT | `{{base_url}}/api/users/2` | Update a user | 200 | `First name is Ivan` (checks `response.name`) | Pass |
| 4 | DELETE | `{{base_url}}/api/users/2` | Delete a user | 204 | `DELETE returns 204` | Pass |
| 5 | GET | `{{base_url}}/api/users/248` | Retrieve a user that was supposedly just created via POST | 404 | `Non-existent user returns 404` | Pass (but see Bug #1) |

## Automated Test Code

**GET user by id:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("First name is Janet", function () {
    const response = pm.response.json();
    pm.expect(response.data.first_name).to.eql("Janet");
});
```

**PUT update user:**
```javascript
pm.test("First name is Ivan", function () {
    const response = pm.response.json();
    pm.expect(response.name).to.eql("Ivan");
});
```
*Note: test name says "First name" but actually checks the `name` field, not `first_name` — left as-is intentionally to document the naming slip made while learning, rather than silently cleaning it up.*

**DELETE user:**
```javascript
pm.test("DELETE returns 204", function () {
    pm.response.to.have.status(204);
});
```

**GET non-existent user (post-POST check):**
```javascript
pm.test("Non-existent user returns 404", function () {
    pm.response.to.have.status(404);
});
```

## Bug Reports / API Behavior Findings

### Finding #1: POST does not persist data (mock behavior)
- **Severity:** Medium (expected for a public sandbox API, but worth documenting explicitly)
- **Related Test Cases:** #1, #5
- **Expected Result:** A user created via POST should be retrievable via a subsequent GET request to the same id
- **Actual Result:** POST returns 201 Created with a generated `id` and `createdAt`, but a following GET to that same id returns 404 — the resource was never actually persisted, only simulated
- **Note:** This is consistent with ReqRes being a mock API for testing/learning purposes rather than a real backend. Documented here as an example of verifying assumptions rather than trusting a 201 response at face value.

### Finding #2: PUT response is missing the resource `id`
- **Severity:** Low
- **Related Test Case:** #3
- **Expected Result:** The PUT response should include the resource's `id`, in addition to the updated fields
- **Actual Result:** The response contains only the submitted fields (`name`, `job`) and `updatedAt` — no `id` field at all, old or new

### Finding #3: Inconsistent response structure between GET and PUT
- **Severity:** Low
- **Related Test Cases:** #2, #3
- **Expected Result:** User data fields should be structured consistently across endpoints for the same resource
- **Actual Result:** GET wraps user fields inside a `data` object (`response.data.first_name`), while PUT returns fields at the top level with no wrapper (`response.name`) — inconsistent schema between endpoints for the same resource

### Finding #4: DELETE is not idempotent
- **Severity:** Medium
- **Related Test Case:** #4
- **Expected Result:** Per HTTP semantics, a second DELETE on an already-deleted resource should return 404 (resource no longer exists)
- **Actual Result:** A repeated DELETE on the same resource returned 204 again, identical to the first call — the API does not check whether the resource still exists before responding

### Finding #5: Responses include unrelated service metadata
- **Severity:** Informational
- **Expected Result:** N/A (informational note, not a functional bug)
- **Actual Result:** All ReqRes responses include a `_meta` block (and sometimes `support`) — promotional/informational content about the API itself, unrelated to the actual user data

## Collection Runner — Final Run

| Metric | Value |
|--------|-------|
| Iterations | 1 |
| Total Tests | 4 |
| Passed | 4 |
| Failed | 0 |
| Errors | 0 |
| Avg. Response Time | 306 ms |

| Request | Status | Tests |
|---------|--------|-------|
| POST create user | 201 | No tests written |
| GET user by id | 200 | Status code is 200 — PASS; First name is Janet — PASS |
| PUT update user | 200 | First name is Ivan — PASS |
| DELETE user | 204 | DELETE returns 204 — PASS |

## Reflections

- POST has no automated test yet — a clear next step would be adding assertions for status code (201) and presence of `id`/`createdAt` fields, following the same pattern used for GET/PUT/DELETE.
- The PUT test name (`"First name is Ivan"`) doesn't match what it actually checks (`response.name`, not `response.data.first_name`) — left intentionally as an honest record of an early mistake rather than corrected after the fact.
- Several of the "bugs" found here are more accurately described as expected limitations of ReqRes as a mock/sandbox API rather than genuine defects — documented with that distinction in mind, since telling the difference between "bug" and "intentional mock behavior" is itself a testing skill.