# API Error Handling — Manual Test Cases & Bug Reports

**Module:** API Error Handling (XQA.io)
**Precondition:** Site is launched, the API Error Handling component is open
**Note:** This component simulates HTTP error responses locally (no live network request is made); test cases verify that each triggered error displays the correct status label and a well-formed JSON error body.

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Trigger 400 Bad Request | 1. Click the "400 Bad Request" card. 2. Observe the displayed status label and JSON body. | Displays "400 Bad Request" with a JSON body containing `error`, `message`, and a `field` indicating which input was invalid | Displayed 400 Bad Request, body: `{"error":"Bad Request","message":"Invalid email format","field":"email"}`, as expected | Pass | Click "400 Bad Request" |
| 2 | Trigger 401 Unauthorized | 1. Click the "401 Unauthorized" card. 2. Observe the displayed status label and JSON body. | Displays "401 Unauthorized" with a JSON body containing `error` and `message` describing missing/invalid authentication | Displayed 401 Unauthorized, body: `{"error":"Unauthorized","message":"Authentication required"}`, as expected | Pass | Click "401 Unauthorized" |
| 3 | Trigger 403 Forbidden | 1. Click the "403 Forbidden" card. 2. Observe the displayed status label and JSON body. | Displays "403 Forbidden" with a JSON body containing `error` and `message` describing lack of permission (distinct from 401 — authenticated but not authorized) | Displayed 403 Forbidden, body: `{"error":"Forbidden","message":"You don't have permission to access this resource"}`, as expected | Pass | Click "403 Forbidden" |
| 4 | Trigger 404 Not Found | 1. Click the "404 Not Found" card. 2. Observe the displayed status label and JSON body. | Displays "404 Not Found" with a JSON body containing `error` and a `message` that references the specific missing resource | Displayed 404 Not Found, body: `{"error":"Not Found","message":"User with ID 999 not found"}`, as expected — message is resource-specific, not generic | Pass | Click "404 Not Found" |
| 5 | Trigger 429 Too Many Requests | 1. Click the "429 Too Many Requests" card. 2. Observe the displayed status label and JSON body. | Displays "429 Too Many Requests" with a JSON body containing `error`, `message`, and a `retryAfter` value indicating when to retry | Displayed 429 Too Many Requests, body: `{"error":"Rate Limited","message":"Too many requests","retryAfter":60}`, as expected | Pass | Click "429 Too Many Requests" |
| 6 | Trigger 500 Internal Server Error | 1. Click the "500 Internal Server Error" card. 2. Observe the displayed status label and JSON body. | Displays "500 Internal Server Error" with a JSON body containing `error`, `message`, and a `requestId` for support/debugging traceability | Displayed 500 Internal Server Error, body: `{"error":"Internal Server Error","message":"An unexpected error occurred","requestId":"abc123"}`, as expected | Pass | Click "500 Internal Server Error" |
| 7 | Trigger 503 Service Unavailable | 1. Click the "503 Service Unavailable" card. 2. Observe the displayed status label and JSON body. | Displays "503 Service Unavailable" with a JSON body containing `error`, `message`, and an `estimatedDowntime` value | Displayed 503 Service Unavailable, body: `{"error":"Service Unavailable","message":"Server is undergoing maintenance","estimatedDowntime":"10 minutes"}`, as expected | Pass | Click "503 Service Unavailable" |
| 8 | Trigger Request Timeout | 1. Click the "Request Timeout" card. 2. Observe the displayed status label and JSON body. | Displays a status label consistent with the "Request Timeout" naming used elsewhere on the page (e.g., "408 Request Timeout"), with a JSON body containing `error` and `message` | Card header displayed "0 Timeout" instead of a proper HTTP status code/name; JSON body: `{"error":"Timeout","message":"Request timed out after 30000ms"}` — body content correct, but header label inconsistent with the rest of the UI | Fail | Click "Request Timeout" |
| 9 | Cross-check error body schema consistency | 1. Trigger all 8 error cards (see #1–#8). 2. Compare the field names present in each JSON body. | All error bodies should share a consistent base schema (`error`, `message`), with additional fields only where contextually meaningful (e.g., `field` for 400, `retryAfter` for 429) | All 8 responses consistently included `error` and `message`; additional fields (`field`, `retryAfter`, `estimatedDowntime`, `requestId`) were present only on the relevant cards and did not leak into unrelated ones | Pass | — |
| 10 | Verify distinct handling of 401 vs 403 | 1. Trigger "401 Unauthorized" (see #2). 2. Trigger "403 Forbidden" (see #3). 3. Compare the two `message` values. | Messages should reflect the semantic difference: 401 = missing/invalid authentication, 403 = authenticated but lacking permission | 401 message referenced "Authentication required"; 403 message referenced lack of permission to access the resource — semantics correctly distinguished | Pass | — |

## Bug Reports

### Bug #1: "Request Timeout" card displays a malformed status label
- **Severity:** Low
- **Module:** API Error Handling
- **Related Test Case:** #8
- **Precondition:** Site is launched, the API Error Handling component is open
- **Expected Result:** The card header should display a proper HTTP status reference, consistent with the format used by every other card on the page (e.g., "408 Request Timeout" or at minimum "Request Timeout" without a leading "0")
- **Actual Result:** The card header displays "0 Timeout" — the leading numeral appears to be an unset/default status code (0) rather than the correct HTTP status code (408) or no numeral at all. The JSON error body itself is correctly formed and unaffected.
- **Steps to Reproduce:**
  1. Open the API Error Handling component.
  2. Locate the timeout card among the error triggers.
  3. Observe the header text reads "0 Timeout" instead of a properly formatted status label.
- **Status:** New