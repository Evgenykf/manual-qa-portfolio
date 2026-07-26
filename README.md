# Manual QA Portfolio — Evgeny

Aspiring QA Engineer | Manual Testing → Automation

## About

I'm learning manual QA testing with the goal of moving into Automation and eventually DevOps. This repository documents my practice on [XQA.io](https://xqa.io), a QA training sandbox, where I test individual UI components and full forms, design test cases, and report bugs the way I would on a real project.

## Testing Approach

Across these test cases, I apply the following techniques:

- **Equivalence partitioning** — testing valid input, invalid input, and empty input as distinct classes for each field
- **Boundary and negative testing** — verifying that actions which should NOT trigger a response actually don't (e.g. right-click shouldn't trigger a left-click message), and testing edge cases like oversized files, unrealistic dates, and malformed data
- **Accessibility testing** — checking keyboard navigation (Tab focus) as a standard part of every component I test, not just mouse interaction
- **Variable isolation** — for components with time-based behavior (Dynamic Properties), I tested timer accuracy, timer reset, and timer independence separately from click-response, to isolate the exact cause of a bug rather than reporting a vague symptom
- **Root cause grouping** — when multiple symptoms share a likely single cause (e.g. both "Add" and "Edit" buttons failing to open a form), I document them as one bug report rather than duplicating tickets
- **Mandatory field verification** — for every form, I explicitly test whether each field is required or optional, rather than assuming

## A Recurring Finding

While testing individual components, I found the same keyboard-navigation issue (Tab not moving focus) reproducing independently on **three components** — Radio Button, Buttons, and Dynamic Properties. However, testing the full Practice Form showed Tab navigation working correctly across the entire form, which narrows this from a site-wide issue to one affecting specific standalone components only. This kind of cross-checking is something I try to apply throughout — treating an early hypothesis as something to verify, not assume.

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

## Tools Used

- Google Sheets (test case drafting)
- Markdown (documentation format)
- Git & GitHub (version control)

## Next Steps

Currently expanding this portfolio with API testing (REST API Playground) and a full end-to-end test plan (Book Store Application), before moving on to test automation with Python/JavaScript and Playwright.