# API Testing — JSONPlaceholder (Postman)

**Tool:** Postman
**API:** [JSONPlaceholder](https://jsonplaceholder.typicode.com)
**Precondition:** Postman installed, `jsonplaceholder` Environment configured

## Setup

**Environment: jsonplaceholder**

| Variable | Value |
|----------|-------|
| `base_url` | `https://jsonplaceholder.typicode.com` |

*Note: the `base_url` variable was created but not consistently used in the saved requests — some requests still use the absolute URL directly rather than `{{base_url}}`. Left as-is to honestly reflect the actual state of the work.*

**Collection:** `jsonplaceholder`, organized by resource lifecycle order: **POST → PUT → GET → DELETE**, plus one additional self-directed check:

1. POST create id
2. PUT update id
3. GET user by id
4. DELETE id
5. **Checking a DELETED id** — added independently (not part of the original exercise) to verify that a GET on a deleted resource correctly returns 404

## Test Cases & Automated Checks

| # | Method | Endpoint | Purpose | Status | Automated Test (pm.test) | Result |
|---|--------|----------|---------|--------|---------------------------|--------|
| 1 | POST | `/posts` | Create a post | 201 | None written — verified manually that the server appended `id: 101` to the submitted `title`, `body`, `userId` | Pass (manual check) |
| 2 | PUT | `/posts/1` | Update a post | 200 | `Status code is 200`, `Title is correct` | Pass |
| 3 | GET | `/posts/1` | Retrieve a post by id | 200 | `Status code is 200`, `Title is correct` | Pass |
| 4 | DELETE | `/posts/1` | Delete a post | 200 | `Status code is 200` | Pass |
| 5 | GET | `/posts/101` | Verify a deleted/non-existent post returns 404 | 404 | `Status code is 404` | Pass |

## Automated Test Code

**GET post by id:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Title is correct", function () {
    const response = pm.response.json();
    pm.expect(response.title).to.eql("sunt aut facere repellat provident occaecati excepturi optio reprehenderit");
});
```

**PUT update post:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});

pm.test("Title is correct", function () {
    const response = pm.response.json();
    pm.expect(response.title).to.eql("Updated title");
});
```

**DELETE post:**
```javascript
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

**GET deleted post (self-directed verification):**
```javascript
pm.test("Status code is 404", function () {
    pm.response.to.have.status(404);
});
```

## Bug Reports / API Behavior Findings

### Finding #1: POST does not persist data (mock behavior)
- **Severity:** Medium (expected for a public sandbox API)
- **Related Test Cases:** #1, #5
- **Expected Result:** A post created via POST should be retrievable via a subsequent GET request to the same id
- **Actual Result:** POST returns 201 Created with a generated `id: 101`, but a following GET to that same id returns 404 — same pattern observed previously on ReqRes

### Finding #2: DELETE returns 200 OK with an empty body, not 204 No Content
- **Severity:** Informational
- **Related Test Case:** #4
- **Expected Result:** N/A — documenting actual behavior, not asserting a defect
- **Actual Result:** DELETE returns `200 OK` with an empty object `{}`, whereas ReqRes returned `204 No Content` with no body for the equivalent operation. Neither is "wrong" — they're simply different conventions between the two APIs.

### Finding #3: 404 response body has no error message
- **Severity:** Informational
- **Related Test Case:** #5
- **Actual Result:** JSONPlaceholder returns an empty object `{}` on 404, with no descriptive `message` field — unlike ReqRes, which typically included a text message describing the error

## Cross-API Comparison: ReqRes vs JSONPlaceholder

| Aspect | ReqRes | JSONPlaceholder |
|--------|--------|------------------|
| Authentication | Required `x-api-key` header (added partway through testing) | None required |
| DELETE success status | 204 No Content, no body | 200 OK, empty body `{}` |
| PUT response includes `id` | No — `id` missing entirely | Yes — `id` present in response |
| Response structure | Wrapped in `data` object; includes `_meta`/`support` blocks | Flat structure, no wrapper, no extra metadata |
| 404 response body | Includes descriptive `message` field | Empty object `{}`, no message |

**Takeaway:** Assumptions about one API's behavior (status codes, response structure, persistence) don't reliably transfer to another API — even when both are used for the same kind of learning exercise. Each service needs to be verified on its own terms rather than assumed to follow the same conventions.

## Collection Runner — Final Run

| Metric | Value |
|--------|-------|
| Iterations | 1 |
| Environment | jsonplaceholder |
| Total Tests | 6 |
| Passed | 6 |
| Failed | 0 |
| Errors | 0 |
| Avg. Response Time | 313 ms |

| Request | Status | Tests |
|---------|--------|-------|
| POST create id | 201 | No tests written |
| PUT update id | 200 | Status code is 200 — PASS; Title is correct — PASS |
| GET user by id | 200 | Status code is 200 — PASS; Title is correct — PASS |
| DELETE id | 200 | Status code is 200 — PASS |
| Checking a DELETED id | 404 | Status code is 404 — PASS |

## Reflections

- Added an extra test (GET on a deleted resource) that wasn't part of the original exercise — a self-directed check to confirm DELETE actually had an effect, rather than assuming it based on the response status alone.
- POST still has no automated test, same gap as in the previous ReqRes session — a clear pattern to address in the next round of practice.
- The `base_url` environment variable was set up but not used consistently across all requests — left as an honest record rather than corrected retroactively.
- The most valuable outcome of repeating this exercise on a second API wasn't confirming the same skills work twice — it was discovering that the two APIs behave differently in several concrete ways (status codes, response structure, persistence details), which reinforced that testing requires verifying actual behavior rather than assuming consistency across systems.