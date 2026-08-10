# Book Store API — Documentation Review

**Module:** Book Store Application → Book Store API (XQA.io)
**Precondition:** Site is launched, the Book Store API reference page is open

**Note on approach:** The Base URL shown (`https://xqa.example.com/api`) uses the reserved `example.com` domain (RFC 2606), which is intentionally non-resolving and used only in documentation. This page is a reference/documentation of an API design, not a live, testable API — it cannot be exercised in Postman or any HTTP client. Testing here is therefore a **documentation review**: checking the listed endpoints for completeness and internal consistency, rather than executing requests and recording Pass/Fail results.

## Endpoints Listed

| Method | Path | Description |
|--------|------|-------------|
| GET | /BookStore/v1/Books | Get all books |
| POST | /BookStore/v1/Books | Add a book |
| DELETE | /BookStore/v1/Book | Delete a book |
| PUT | /BookStore/v1/Books/{ISBN} | Replace a book |
| POST | /Account/v1/User | Create User |
| POST | /Account/v1/GenerateToken | Generate Token |

## Review Findings

| # | Finding | Details |
|---|---------|---------|
| 1 | Inconsistent resource naming: `Book` vs `Books` | GET, POST, and PUT all use the plural `/BookStore/v1/Books`. DELETE alone uses the singular `/BookStore/v1/Book`, breaking the naming pattern used elsewhere in the same API |
| 2 | DELETE is missing an identifier for which book to delete | The description says "Delete a book" (a specific book), but the path has no `{ISBN}` or any other identifier — unlike PUT, which correctly includes `{ISBN}` for the same purpose ("Replace a book"). As documented, there is no way to specify which book DELETE should act on |

## Recommended Correction

```
DELETE /BookStore/v1/Books/{ISBN}
```
— aligning both the plural resource name and the missing identifier with the pattern already used by PUT.

## Bug Reports

### Bug #1: DELETE endpoint path is inconsistent and missing a required identifier
- **Severity:** Medium
- **Module:** Book Store Application → Book Store API (documentation)
- **Related Finding:** #1, #2
- **Precondition:** Site is launched, the Book Store API reference page is open
- **Expected Result:** The DELETE endpoint should follow the same naming convention as the other Books endpoints (`/BookStore/v1/Books`) and include an identifier (e.g. `{ISBN}`) to specify which book to delete, consistent with how PUT is documented
- **Actual Result:** DELETE is documented as `/BookStore/v1/Book` (singular, inconsistent with GET/POST/PUT) and has no identifier in the path, making it impossible to determine which book the endpoint would act on
- **Steps to Reproduce:**
  1. Open the Book Store API reference page.
  2. Compare the DELETE endpoint's path and description against the GET, POST, and PUT endpoints for the same resource.
  3. Observe the naming and missing-identifier inconsistency.
- **Status:** New