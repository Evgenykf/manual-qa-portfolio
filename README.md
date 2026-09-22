# Manual QA Portfolio — Evgeny

Aspiring QA Engineer | Manual Testing → Automation

## About

I'm learning manual QA testing with the goal of moving into Automation and eventually DevOps. This repository documents my practice on [XQA.io](https://xqa.io), a QA training sandbox, hands-on API testing in Postman, SQL practice in PostgreSQL, and — now — Python fundamentals as I move toward test automation with Pytest and Playwright.

## Testing Approach

Across these test cases, I apply the following techniques:

- **Equivalence partitioning** — testing valid input, invalid input, and empty input as distinct classes for each field
- **Boundary and negative testing** — verifying that actions which should NOT trigger a response actually don't, and testing edge cases like oversized files, unrealistic dates, and malformed data
- **Accessibility testing** — checking keyboard navigation (Tab focus) as a standard part of every component I test, not just mouse interaction
- **Variable isolation** — for components with time-based behavior (Dynamic Properties), I tested timer accuracy, timer reset, and timer independence separately from click-response, to isolate the exact cause of a bug rather than reporting a vague symptom
- **Root cause grouping** — when multiple symptoms share a likely single cause (e.g. both "Add" and "Edit" buttons failing to open a form), I document them as one bug report rather than duplicating tickets
- **Mandatory field verification** — for every form, I explicitly test whether each field is required or optional, rather than assuming
- **API state and consistency testing** — checking whether created/updated/deleted resources persist correctly, whether operations are idempotent, and whether response schemas are consistent across related endpoints
- **Automated assertions (Postman)** — writing `pm.test()` checks in JavaScript to verify status codes and response data automatically, instead of only inspecting responses manually
- **Cross-API verification** — repeating the same testing approach on a second, independent API (JSONPlaceholder after ReqRes) to confirm the underlying skill transfers, rather than just repeating memorized steps
- **Protocol-aware testing** — adapting my approach for WebSocket's persistent, bidirectional connection model rather than applying REST-style request/response assumptions
- **API design/documentation review** — when a listed API turns out to be a non-executable reference (reserved `example.com` domain), switching from Pass/Fail execution to reviewing endpoint naming consistency and completeness instead
- **Data-driven verification (SQL)** — designing normalized schemas with justified data types (e.g. `DECIMAL` over `FLOAT` for money, `TIMESTAMPTZ` over `TIMESTAMP` for time-zone-aware dates), and using `JOIN`/`LEFT JOIN` + `IS NULL` to find orphaned records directly at the data layer, rather than trusting only what the application UI shows

## Recurring Findings

While testing individual components, I found the same keyboard-navigation issue (Tab not moving focus) reproducing independently on **three components** — Radio Button, Buttons, and Dynamic Properties. However, testing the full Practice Form showed Tab navigation working correctly across the entire form, which narrows this from a site-wide issue to one affecting specific standalone components only.

A second, unrelated pattern showed up in API testing: **inconsistent response schemas**. The REST API Playground returns different field sets for the same user depending on the endpoint, the WebSocket Playground uses four different keys to identify message kind across its responses, and the Book Store API documentation itself mixes singular/plural resource naming (`Book` vs `Books`) between its own endpoints. Different parts of the same practice site, same category of issue — worth noting as a recurring theme rather than isolated, unrelated findings.

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
| ReqRes (Postman) | [api-testing/reqres-postman-tests.md](./api-testing/reqres-postman-tests.md) | Full CRUD cycle tested with automated `pm.test()` assertions, organized into a Collection with environment variables, executed via Collection Runner (4/4 tests passing). Found DELETE is not idempotent and inconsistent response structure between GET and PUT |
| JSONPlaceholder (Postman) | [api-testing/jsonplaceholder-postman-tests.md](./api-testing/jsonplaceholder-postman-tests.md) | Second CRUD cycle on a different API to confirm skills transfer, not just repeat steps — found different conventions from ReqRes, reinforcing that API behavior must be verified per-service rather than assumed |
| WebSocket Testing (XQA) | [api-testing/websocket-testing.md](./api-testing/websocket-testing.md) | Server accepts malformed/unrecognized messages without validation; inconsistent message-type key across different server responses |
| API Error Handling (XQA) | [api-testing/api-error-handling.md](./api-testing/api-error-handling.md) | All 8 error responses (400–503) correctly structured and semantically distinct (e.g. 401 vs 403); one UI bug found — malformed status label on the Request Timeout card |

### Book Store Application (end-to-end scenario)

| Module | File | Key Findings |
|--------|------|---------------|
| Login | [book-store-app/login.md](./book-store-app/login.md) | Verified "no valid credentials" behavior is intentional (registration disabled), not a defect — confirmed rather than assumed |
| Book Store (search) | [book-store-app/book-store.md](./book-store-app/book-store.md) | Search does not check the Publisher column at all; usability gaps in substring/whitespace matching |
| Profile | [book-store-app/profile.md](./book-store-app/profile.md) | "Register" link redirects to the same destination as "Login" instead of a distinct registration flow |
| Book Store API (docs) | [book-store-app/api.md](./book-store-app/api.md) | Recognized a non-executable reference API (reserved domain) and switched to a documentation review — found inconsistent `Book`/`Books` naming and a missing identifier on the DELETE endpoint |

### SQL

| Topic | File | Content |
|-------|------|---------|
| Basics | [sql/basics.md](./sql/basics.md) | Designed 4 related tables (users, products, orders, order_items) — justified data type choices (`DECIMAL` over `FLOAT` for money, `TIMESTAMPTZ` over `TIMESTAMP`), `PRIMARY KEY` / `FOREIGN KEY` |
| Filtering | [sql/filtering.md](./sql/filtering.md) | `WHERE`, `BETWEEN`, `IN`, `LIKE`/`ILIKE`, `AND`/`OR` precedence, computed columns via `AS` |
| JOIN | [sql/join.md](./sql/join.md) | `INNER JOIN` vs `LEFT JOIN`, filtering in `WHERE` vs `ON`, multi-table chains, finding orphaned records (`LEFT JOIN` + `IS NULL`) |
| UNION | [sql/union.md](./sql/union.md) | Combining independent `SELECT` queries, `UNION ALL`, sorting after combination |
| Aggregation | [sql/aggregation.md](./sql/aggregation.md) | `GROUP BY` + `COUNT`/`SUM`, difference between `WHERE` (pre-grouping) and `HAVING` (post-grouping) |

### QA Automation — Python (in progress)

| Topic | File | Content |
|-------|------|---------|
| Basics | [automation/python/basics.md](./automation/python/basics.md) | Variables, types, boolean vs string status flags, type coercion pitfalls (`"200" == 200`) |
| Control Flow | [automation/python/control-flow.md](./automation/python/control-flow.md) | `if`/`elif`/`else` branching, short-circuit evaluation, loop increment ordering |
| Lists & Strings | [automation/python/lists-and-strings.md](./automation/python/lists-and-strings.md) | Slicing, `.split()`, negative indexing for structure-independent parsing, variable overwrite pitfalls |
| Tuples, Dicts, Sets | [automation/python/tuples-dicts-sets.md](./automation/python/tuples-dicts-sets.md) | Immutability, dict key lookups vs value checks, set operations, combining all three to summarize a test run |
| Functions | [automation/python/functions.md](./automation/python/functions.md) | `def`, return values vs side effects, variable scope, `sorted(key=lambda...)` |
| Files & Exceptions | [automation/python/files-exceptions.md](./automation/python/files-exceptions.md) | File I/O, `with...as`, `try`/`except` for handling flaky test elements and malformed API responses |
| Modules | [automation/python/modules.md](./automation/python/modules.md) | Custom modules, `from...import` vs `import`, built-in `random`/`datetime` |
| OOP & Decorators | [automation/python/oop-and-decorators.md](./automation/python/oop-and-decorators.md) | Classes, constructors, inheritance (`super()`), polymorphism, decorators — direct foundation for Page Object Model |

## Tools Used

- Google Sheets (test case drafting)
- Markdown (documentation format)
- Git & GitHub (version control)
- Postman (API testing, automated assertions, Collections, Environment variables, Collection Runner)
- WebSocket testing (connection lifecycle, message validation, protocol-level testing)
- SQL (PostgreSQL, DBeaver) — schema design, filtering, joins, aggregation
- Python — syntax, OOP, working toward test automation

## Next Steps

Manual QA and SQL fundamentals are complete. Currently building the Python foundation for QA Automation: Pytest & test design (fixtures, mocking, parametrization) next, then API automation with `requests`, and Playwright for UI automation (Page Object Model) — chosen for its strong alignment with Python and remote-friendly product companies.