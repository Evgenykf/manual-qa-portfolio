# Book Store Application — Search — Manual Test Cases & Bug Reports

**Module:** Book Store Application → Book Store (search) (XQA.io)
**Precondition:** Site is launched, the Book Store page is open, book list is visible

## Test Cases

| # | Test Name | Description | Expected Result | Actual Result | Pass/Fail | Test Data |
|---|-----------|--------------|------------------|----------------|-----------|-----------|
| 1 | Search by Title (exact match) | 1. Type a full book title into the search box. | The list should filter to show only the matching book(s) | List correctly filtered to show the matching book | Pass | "Learning JavaScript Design Patterns" |
| 2 | Search by Author (exact match) | 1. Type a full author name into the search box. | The list should filter to show only the matching book(s) | List correctly filtered to show the matching book | Pass | "Addy Osmani" |
| 3 | Search by Author (partial, from the start) | 1. Type only the beginning of an author's name. | The list should filter to show matching book(s), consistent with the search's matching behavior | List correctly filtered to show the matching book | Pass | "ad" |
| 4 | Search by Publisher (exact/partial, from the start) | 1. Type a publisher name (or the start of one) into the search box. | The list should filter to show book(s) with that publisher, since Publisher is a visible, searchable column in the same table | No results shown for any variation tested — list appears empty | Fail | "O'Reilly", "Reilly", "No Starch" |
| 5 | Search by Author — substring not at the start of the word | 1. Type a fragment of an author's name that is not the first characters (e.g. the second half of a name). | *(See Note below — behavior depends on intended search type: "starts with" vs "contains")* | No results shown; only text matching from the very start of a field returns results | Fail (usability) | "Osmani" (from "Addy Osmani") |
| 6 | Search with leading or trailing space | 1. Type a valid search term with an extra space before it, or after it. | *(See Note below — ideally the input should be trimmed before matching)* | No results shown once a leading or trailing space is present, even though the same term without the space matches correctly | Fail (usability) | " Addy", "Addy " |

**Note on Test Cases 5 & 6:** There is no explicit specification stating whether the search should match substrings anywhere in the text (`contains`) or only from the beginning (`starts with`), nor whether input should be trimmed of extra spaces. Because of this, these two findings are logged as **usability observations** rather than confirmed defects — see Bug #2 below.

## Bug Reports

### Bug #1: Search does not return results for the Publisher column
- **Severity:** Medium
- **Module:** Book Store Application → Book Store (search)
- **Related Test Case:** #4
- **Precondition:** Site is launched, the Book Store page is open
- **Expected Result:** Typing a publisher name that exactly matches the start of a value in the Publisher column (e.g. "O'Reilly", matching "O'Reilly Media") should filter the list to show matching books, consistent with how Title and Author search behave
- **Actual Result:** No results are returned for any tested Publisher value ("O'Reilly", "Reilly", "No Starch"), even "O'Reilly" which is an exact match for the start of the visible text in that column. Search appears to not check the Publisher field at all
- **Steps to Reproduce:**
  1. Open the Book Store page.
  2. Type "O'Reilly" into the search box (a value visible in the Publisher column of multiple rows).
  3. Observe that the list shows no results, despite matching entries being visible in the table before typing.
- **Status:** New

### Bug #2: Search behavior does not match common UX conventions (usability)
- **Severity:** Low
- **Module:** Book Store Application → Book Store (search)
- **Related Test Cases:** #5, #6
- **Precondition:** Site is launched, the Book Store page is open
- **Expected Result:** Not formally specified on the page; flagged for review because most users expect search to (a) match text anywhere within a field, not only from the start, and (b) ignore leading/trailing whitespace
- **Actual Result:**
  1. Typing a substring that is not at the start of a field (e.g. "Osmani" from "Addy Osmani") returns no results, while typing from the start (e.g. "Addy" or "ad") works correctly.
  2. Typing a valid search term with an extra leading or trailing space (e.g. " Addy" or "Addy ") returns no results, even though the same term without the space matches correctly.
- **Steps to Reproduce:**
  1. Type "Osmani" into the search box — no results appear, despite "Addy Osmani" being a valid author in the list.
  2. Clear the search box, type " Addy" (with a leading space) — no results appear, despite "Addy" alone matching correctly.
- **Status:** New (for review — may be intended behavior; recommend confirming expected search spec)