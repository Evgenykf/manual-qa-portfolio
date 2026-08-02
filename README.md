# Manual QA Portfolio — Evgeny

Aspiring QA Engineer | Manual Testing → Automation

## About

I'm learning manual QA testing with the goal of moving into Automation and eventually DevOps. This repository documents my practice on [XQA.io](https://xqa.io), a QA training sandbox, as well as hands-on API testing in Postman, where I test individual UI components, full forms, REST APIs, and WebSocket connections, design test cases, write automated checks, and report bugs the way I would on a real project.

## Testing Approach

Across these test cases, I apply the following techniques:

- **Equivalence partitioning** — testing valid input, invalid input, and empty input as distinct classes for each field
- **Boundary and negative testing** — verifying that actions which should NOT trigger a response actually don't, and testing edge cases like oversized files, unrealistic dates, and malformed data
- **Accessibility testing** — checking keyboard navigation (Tab focus) as a standard part of every component I test, not just mouse interaction
- **Variable isolation** — for components with time-based behavior (Dynamic Properties), I tested timer accuracy, timer reset, and timer independence separately from click-response, to isolate the exact cause of a bug rather than reporting a vague symptom
- **Root cause grouping** — when multiple symptoms share a likely single cause (e.g. both "Add" and "Edit" buttons failing to open a form), I document them as one bug report rather than duplicating tickets
- **Mandatory field verification** — for every form, I explicitly test whether each field is required or optional, rather than assuming
- **API state and consistency testing** — checking whether created/updated/deleted resources persist correctly, whether operations are idempotent (e.g. a repeated DELETE should return 404, not 204 again), and whether response schemas are consistent across related endpoints
- **Automated assertions (Postman)** — writing `pm.test()` checks in JavaScript to verify status codes and response data automatically, instead of only inspecting responses manually
- **Cross-API verification** — repeating the same testing approach on a second, independent API (JSONPlaceholder after ReqRes) to confirm the underlying skill transfers, rather than just repeating memorized steps — and documenting where the two services actually behave differently
- **Protocol-aware testing** — adapting my approach for WebSocket's persistent, bidirectional connection model (connection lifecycle, server-initiated push messages, message ordering) rather than applying REST-style request/response assumptions

## Recurring Findings

While testing individual components, I found the same keyboard-navigation issue (Tab not moving focus) reproducing independently on **three components** — Radio Button, Buttons, and Dynamic Properties. However, testing the full Practice Form showed Tab navigation working correctly across the entire form, which narrows this from a site-wide issue to one affecting specific standalone components only.

A second, unrelated pattern showed up in API testing: **inconsistent response schemas**. The REST API Playground returns different field sets for the same user depending on the endpoint (`/api/users` vs `/api/users/1`), and the WebSocket Playground uses four different keys (`type`, `action`, `status`, none at all) to identify message kind across its responses. Different mock service, same category of issue — worth noting as a recurring theme across this practice site rather than isolated, unrelated findings.

Both patterns reflect the same underlying habit: cross-checking a hypothesis across multiple components/endpoints instead of treating a single observation as conclusive.

## Test Cases & Bug Reports

### Elements

| Module | File | Key Findings |
|--------|------|---------------|
| Text Box | [elements/text-box.md](./elements/text-box.md) | Missing validation on Name and Address fields |
| Check Box | [elements/check-box.md](./elements/check-box.md) | Parent/child checkbox state desync |
| Radio Button | [elements/radio-button.md](./elements/radio-button.md) | "No" option unselectable; keyboard navigation broken |
| Web Tables | [elements/web-tables.md](./elements/web-tables.md) | Add/Edit forms don't open; Page field not editable |
| Buttons | [elements/buttons.md](./elements/buttons.md) | Keyboard navigation broken (same pattern as Radio Button) |
| Dynamic Properties | [elements/dynamic-properties.md](./elements/dynamic-properties.md) | Elements change visual state but remain non-clickable |

### Forms

| Module | File | Key Findings |
|--------|------|---------------|
| Practice Form | [forms/practice-form.md](./forms/practice-form.md) | 38 test cases across 12 fields; 7 bugs found, including a Critical severity issue (executable file accepted as a picture upload) and a broken State→City dependency |

### API Testing

| Module | File | Key Findings |
|--------|------|---------------|
| REST API Playground (XQA) | [api-testing/rest-api-playground.md](./api-testing/rest-api-playground.md) | Mock API does not persist state between requests (POST/PUT/DELETE don't affect subsequent responses); inconsistent schema between endpoints |
| ReqRes (Postman) | [api testing/reqres-postman-tests.md](./api-testing/reqres-postman-tests.md) | Full CRUD cycle tested with automated `pm.test()` assertions, organized into a Collection with environment variables, executed via Collection Runner (4/4 tests passing). Found DELETE is not idempotent and inconsistent response structure between GET and PUT |
| JSONPlaceholder (Postman) | [api-testing/jsonplaceholder-postman-tests.md](./api-testing/jsonplaceholder-postman-tests.md) | Second CRUD cycle on a different API to confirm skills transfer, not just repeat steps — found different conventions from ReqRes (status codes, response structure), reinforcing that API behavior must be verified per-service rather than assumed |
| WebSocket Testing (XQA) | [api-testing/websocket-testing.md](./api-testing/websocket-testing.md) | Server accepts malformed/unrecognized messages without validation; inconsistent message-type key across different server responses |

## Tools Used

- Google Sheets (test case drafting)
- Markdown (documentation format)
- Git & GitHub (version control)
- Postman (API testing, automated assertions, Collections, Environment variables, Collection Runner)
- WebSocket testing (connection lifecycle, message validation, protocol-level testing)

## Next Steps

Currently expanding this portfolio with focused Accessibility Testing (Screen Reader, Color Contrast, Skip Links) and a full end-to-end test plan (Book Store Application), before moving on to test automation with Python/JavaScript and Playwright.
