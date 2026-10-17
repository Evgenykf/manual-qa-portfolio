# Browser Basics

## Overview

Browser behavior affects navigation, page rendering, cached resources, form submission, and the state users see when moving between pages or tabs. For manual QA, it is important to distinguish the behavior that can be observed from a possible explanation for it.

## 1. Browser, Website, and Server

- **Browser** — an application such as Chrome that requests resources, processes page content, and provides the interface users interact with.
- **Website** — a collection of pages, resources, and functionality.
- **Server** — a system that processes requests and returns resources or data.
- **Frontend** — the user-facing part of an application, including its interface and client-side logic.
- **Client** — in this context, the browser and the application code running on the user's device.

A server does not necessarily send a finished picture of a page. The browser processes HTML, CSS, JavaScript, and other resources to render the interface.

From a QA perspective, a visible but unresponsive button is an observable symptom. It does not, by itself, identify whether the cause is in client-side code, network communication, or server-side behavior.

## 2. URL, DNS, and HTTPS

Consider this URL:

`https://example.com/login`

- `https://` is the scheme.
- `example.com` is the hostname.
- `/login` is the path.

**DNS** helps resolve a hostname to a network address. The path, such as `/login`, is not part of the hostname resolved by DNS.

**HTTPS** uses TLS to protect the confidentiality and integrity of data in transit and to help authenticate the server.

### Redirect observation

Opening a link from `https://example.com/` led to `https://www.iana.org/help/example-domains`. Entering the non-`www` IANA URL also ended at the `www` version.

The final URL was observed to differ from the entered URL. However, the full request chain was not inspected in Network, so an HTTP redirect and a `Location` response header were not confirmed.

## 3. Browser Navigation and History

The following Chrome behaviors were observed:

- Following a link navigated from `example.com` to the IANA example-domains page.
- Back returned to the previous page, and Forward returned to the later page.
- Reload refreshed the current page without a visible problem in the tested examples.
- Hard Reload did not produce a noticeable visual difference on `example.com`.
- Opening a link in a new tab created a separate tab history; Back was unavailable when that navigation was the first entry in the new tab.

Reload generally refreshes the current history entry rather than adding a new navigation entry. Each tab maintains its own navigation history.

A Hard Reload producing the same visible page does not, by itself, prove that every cached resource was bypassed.

## 4. Page Source, Rendering, and Loading

Viewing the IANA page source displayed HTML markup and code rather than a rendered screenshot. When the browser window was narrowed, the text adjusted to the available width.

At a high level:

- **HTML** describes page structure and content.
- **CSS** controls presentation and layout.
- **JavaScript** can change the interface and its behavior.
- The browser processes the available content and resources to render the page.

During a Wikipedia page load, text appeared before some photographs. On a later visit, the photographs appeared faster. Different resources can load or be processed at different rates; this observation does not establish that the server always sends all text before all images.

### Dynamic loading

On the test page `https://the-internet.herokuapp.com/dynamic_loading/1`, selecting **Start** displayed a `Loading` message and indicator before `Hello World!` appeared.

This demonstrates that a page can already be rendered while an operation is still in progress. For QA, distinguish:

- **Rendered** — content is displayed.
- **Interactive** — controls behave as expected.
- **Completed** — the operation being checked has finished.

The exercise demonstrated dynamic loading; it did not reveal an actual network or server failure.

## 5. Browser Cache and Page Restoration

### Memory cache and disk cache

A repeat visit to Wikipedia showed `disk cache` for an image request. During a Hard Reload, Network showed entries from both `memory cache` and `disk cache`.

After enabling **Disable cache**, clearing the Network log, and reloading, the reported result included one `memory cache` entry and zero `disk cache` entries. The remaining entry was not investigated at the individual-request level, so its cause was not established.

Key distinctions:

- **Memory cache** stores resources in browser memory.
- **Disk cache** stores resources on disk.
- Cached resources can include images, CSS, and JavaScript.
- Hard Reload is not the same as clearing all cached data.
- Chrome's **Empty Cache and Hard Reload** provides a more explicit way to clear the browser cache and reload.

The observations did not prove that caching was disabled for every resource or establish the single cause of faster image loading.

### Back/Forward Cache (bfcache)

On the dynamic-loading test page, `Start` was selected and `Hello World!` appeared. After navigating to `example.com` and pressing Back, the previous URL and `Hello World!` state were restored. Reloading the test page returned it to the initial state with the `Start` button.

This is consistent with the browser restoring a previous page state. One possible mechanism is the **Back/Forward Cache (bfcache)**, which can preserve a page and its state for history navigation. The use of bfcache was not independently verified.

Resource caching and restoring a whole page during Back/Forward navigation are different mechanisms.

### Potential stale resources

If a site's styling appears outdated until Hard Reload, cached CSS or other resources may be involved. This is a diagnostic hypothesis, not a confirmed cause unless the relevant resources and cache behavior are inspected.

## 6. Tabs, Incognito, and UI State

- Incognito uses a separate temporary browser state; authentication from a regular Chrome window is not automatically shared with it.
- Incognito does not make a user invisible to a website or server.
- Separate tabs can retain different scroll positions and interface states.
- A tab may continue to display previously loaded data until it is refreshed or the application updates it.

A stale value in one tab does not, on its own, prove server delay or a validation problem. The cause requires further investigation. Similarly, a page restored by Back may show an earlier interface state that does not necessarily reflect the application's latest data.

## 7. Forms and Standard Browser Behavior

### Required-field validation

On a text-input test form, the fields were set to required. After the `Text` field was cleared and **Submit** was selected, Chrome displayed a validation message and prevented submission because a required field was empty.

This confirmed native HTML form validation for the tested field and form. It was not a complete test of all field types or validation rules.

An earlier form exercise changed the URL to a submit path but did not demonstrate validation of a text field, because the tested form did not contain one. That result should not be treated as evidence for or against required-field validation.

### POST resubmission warning

After submitting a form on a test page, the URL changed from `/pages/forms/html-form/` to `/pages/forms/html-form/submit`. Reload displayed Chrome's **Confirm Form Resubmission** dialog.

The warning indicates that reloading may repeat a submission made with previously entered data. It is standard browser behavior and does not, by itself, prove that a duplicate operation occurred. The test was cancelled, so duplicate creation was not verified.

The **Post/Redirect/Get (PRG)** pattern can be used by applications to redirect after processing a POST and reduce accidental resubmission on Reload.

### Keyboard focus and Enter

Pressing Enter can submit a form in some circumstances, such as when focus is in a single-line input. The exact behavior depends on the form structure and JavaScript handlers. This behavior was discussed but not completed as a separate practical test.

## 8. Manual QA Investigation Principles

When investigating unexpected browser behavior, separate three levels:

1. **Observation** — what actually happened.
2. **Hypothesis** — a possible explanation.
3. **Confirmed cause** — an explanation supported by additional evidence.

Examples:

| Observation | Possible explanations to investigate |
|---|---|
| Text appears before images | Resources may load or render at different rates |
| `Loading` never disappears | Client-side logic, a network request, server behavior, or application logic may be involved |
| Previous state remains after Back | The browser may have restored the page state |
| A second tab shows an old value | The tab may still display previously loaded application state |
| Chrome warns about form resubmission | Reload may repeat a previous form submission; duplicate effects need separate verification |
| A page looks incorrect | Inspect the evidence before attributing the issue to the browser, network, or server |

A useful QA report records the exact steps, expected behavior, actual behavior, and available evidence. Do not state a root cause based only on a symptom.

## Scope of Practical Checks

The practice covered browser navigation, page source and responsive text layout, dynamic loading, cache indicators in Chrome DevTools, Back/Forward state restoration, incognito behavior, tab state, required-field validation, and the form resubmission warning.

The following points were not conclusively verified: the exact HTTP redirect chain for the IANA URL, whether bfcache was responsible for restoring the dynamic page, the reason for the remaining memory-cache entry, the cause of a hypothetical stale profile value, and whether resubmitting the form would create a duplicate server-side operation.
