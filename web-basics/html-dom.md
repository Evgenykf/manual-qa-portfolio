# HTML, DOM and Chrome DevTools

## Overview

This document covers the HTML and DOM concepts used when inspecting web pages during manual QA. It focuses on reading the current document structure, understanding element attributes and relationships, locating elements with CSS selectors, and checking visible behavior against the underlying markup and network activity.

## HTML Source and the DOM

- **HTML source** is the markup available from the page source.
- **DOM (Document Object Model)** is the browser's object representation of the document, organized as a tree of nodes and elements.
- The DOM can change after the page loads as the application responds to user actions. Because of this, the current tree shown in **Chrome DevTools → Elements** may differ from the original HTML source.
- A **parent** contains another element; a **child** is nested inside a parent; **siblings** share the same parent.
- The DOM hierarchy describes structural relationships, not necessarily the visual position of elements on the page.
- CSS pseudo-elements such as `::before` and `::after` are not ordinary HTML tags, even when they display generated content.

## Inspecting Elements in Chrome DevTools

The **Elements** panel can be used to inspect the current HTML structure and read an element's tag, attributes, text, and relationship to surrounding elements.

For example, the Login page at `https://the-internet.herokuapp.com/login` contains a username field similar to:

```html
<div class="large-6 small-12 columns">
  <label for="username">Username</label>
  <input type="text" name="username" id="username">
</div>
```

In this example:

- The `div` is the parent element.
- The `label` and `input` are children of the `div` and siblings of each other.
- The `for="username"` value associates the label with the input whose `id` is `username`.
- The `class` attribute contains three class names: `large-6`, `small-12`, and `columns`.

A Login button on the same page was inspected:

```html
<button class="radius" type="submit">
  <i class="fa fa-2x fa-sign-in"> Login</i>
</button>
```

The `button` element has `type="submit"`; the nested `i` element contains the displayed text and styling classes. Markup inspection helps identify how an element is represented, but does not by itself prove that the associated action succeeds.

## Common HTML Attributes

| Attribute | Purpose |
|---|---|
| `id` | Identifies an element within the document; commonly used as a locator. |
| `class` | Associates an element with one or more CSS classes. Multiple class names are separated by spaces. |
| `name` | Gives a form control a name, often used when form data is submitted. |
| `type` | Defines the type or behavior of controls such as `input` and `button`. |
| `placeholder` | Displays a hint in an empty input; it is not a substitute for a visible label. |
| `required` | Marks a form control as required for native HTML form validation. It does not mean the submit button must be disabled. |
| `href` | Specifies the destination or reference for a link. |
| `src` | Specifies the resource URL for elements such as images. |
| `alt` | Provides alternative text for an image; its appropriate value depends on the image's purpose. |
| `checked` | Boolean attribute that sets a checkbox's or radio button's initial checked state in the HTML markup. |
| `disabled` | Boolean attribute that disables a form control when present. |

Attribute values are useful evidence when identifying an element, but their meaning and stability should be evaluated in the context of the page.

## CSS Selector Basics

CSS selectors can be tested in Chrome DevTools using **Elements → Ctrl+F**. The following selectors were checked against the current DOM of the Login page:

| Selector | What it matches |
|---|---|
| `button` | Elements with the `button` tag. |
| `button[type="submit"]` | `button` elements with `type="submit"`. |
| `button.radius` | `button` elements with the class `radius`. |
| `#username` | The element with `id="username"`. |
| `#password` | The element with `id="password"`. |
| `input[name="password"]` | `input` elements whose `name` attribute is `password`. |
| `.flash.error` | An element that has both the `flash` and `error` classes. |
| `a[href="#delete"]` | Links whose `href` attribute is `#delete`. |

The selector search showed one match for `button[type="submit"]`, `button.radius`, `#username`, `#password`, and `input[name="password"]` at the time of inspection. Searching for `button` showed multiple matches (`1 of 9`). These counts describe the current DOM at the time of the check; they do not guarantee that a locator will remain unique after the application changes.

### Uniqueness and Locator Choice

Two different selectors, `#password` and `input[name="password"]`, each matched one element during the inspection. That result alone does not prove that one selector is more reliable than the other. A longer selector is not automatically better: a useful locator should express the intended target clearly and rely on attributes that are meaningful and stable for the application.

For repeated controls, a selector may identify the correct type of action without identifying the correct record. For example, `a[href="#delete"]` may match Delete links in several table rows. The target record must first be identified, then the action should be selected within that record's context.

## Inspecting Dynamic Feedback

On `https://the-internet.herokuapp.com/login`, submitting the username `tomsmith` with an incorrect password displayed the message `Your password is invalid!`. The message element was inspected as:

```html
<div data-alert="" id="flash" class="flash error">
  Your password is invalid!
  <a href="#" class="close">×</a>
</div>
```

Observed behavior:

- The message container had `id="flash"` and the classes `flash error`.
- It contained a close link (`a.close`) displayed as `×`.
- Selecting the close link made the message disappear, and the page URL did not change.
- After closing the message, searching for `#flash` in Elements returned `0 of 0`.

These observations show how to inspect a dynamic message before and after interaction. The search result alone does not establish the exact implementation used to hide or remove the element.

## Table Rows and Contextual Actions

On `https://the-internet.herokuapp.com/tables`, a data row for John Smith included values such as `jsmith@gmail.com`, followed by `edit` and `delete` links:

```html
<tr>
  <td>Smith</td>
  <td>John</td>
  <td>jsmith@gmail.com</td>
  <td>$50.00</td>
  <td>http://www.jsmith.com</td>
  <td>
    <a href="#edit">edit</a>
    <a href="#delete">delete</a>
  </td>
</tr>
```

The row uses `td` cells for data, unlike a header row that uses `th` cells. The record can be identified from its values, such as the email address, and the relevant action can then be located inside that row. The link text suggests the intended action, but the text alone is not proof of what happens after clicking it.

## Images and Network Inspection

On `https://the-internet.herokuapp.com/broken_images`, an image element was found with the following markup:

```html
<img src="asdf.jpg">
```

The related request was observed in Chrome DevTools → Network with status `404 Not Found`.

This links an element's `src` attribute to a network request and its response status. A `404` indicates that the requested resource was not found at that address, but the status alone does not establish the root cause. The observed image tag also had no `alt` attribute; whether an image requires meaningful alternative text depends on its role and purpose.

## Checking Expected Behavior

On `https://the-internet.herokuapp.com/add_remove_elements/`, the expected result was defined before testing:

1. Open the page with no Delete buttons displayed.
2. Click **Add Element** twice.
3. Verify that two **Delete** buttons appear.
4. Click one **Delete** button.
5. Verify that one **Delete** button remains.

The reported result matched the expected behavior, so this check was recorded as **PASS**.

On `https://the-internet.herokuapp.com/dynamic_controls`, the button markup was observed changing from:

```html
<button autocomplete="off" type="button" onclick="swapInput()">Enable</button>
```

to:

```html
<button autocomplete="off" type="button" onclick="swapInput()">Disable</button>
```

The message `It's enabled!` was also observed. This demonstrates that an interaction can change the displayed text and the DOM state visible in Elements. When testing dynamic controls, the button label and the actual state of the related input should be checked as separate pieces of evidence.

## Manual QA Takeaways

- Use **Elements** to inspect the current DOM, not just the original HTML source.
- Read element attributes and surrounding structure before choosing a locator.
- Treat a match count such as `1 of 1` as evidence of current uniqueness, not a guarantee of long-term stability.
- For repeated controls, identify the correct record before locating its action.
- Compare UI behavior with the underlying element and, where relevant, with the corresponding Network request.
- Define the expected result before functional checks.
- Separate observations from hypotheses. A disappearing message, a `404` response, or a missing attribute is an observation; identifying the root cause usually requires additional evidence.
