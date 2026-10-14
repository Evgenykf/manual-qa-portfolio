# HTTP — Web Basics / Manual QA

HTTP (Hypertext Transfer Protocol) is a protocol for exchanging messages between a client and a server. This document covers core HTTP concepts, their relevance to Manual QA, and practical observations made in Chrome DevTools → Network.

Examples were performed using public test services, including `httpbin.org`, `httpbingo.org`, and `the-internet.herokuapp.com`. These services are useful for learning about network communication; responses from echo services do not prove that real business resources were created, modified, or deleted.

## Contents

1. [HTTP Requests and Responses](#1-http-requests-and-responses)
2. [URLs and Parameters](#2-urls-and-parameters)
3. [HTTP Methods](#3-http-methods)
4. [Request Body and Response Body](#4-request-body-and-response-body)
5. [HTTP Headers](#5-http-headers)
6. [Cookies in HTTP](#6-cookies-in-http)
7. [Status Codes](#7-status-codes)
8. [Redirects](#8-redirects)
9. [Caching and Conditional Requests](#9-caching-and-conditional-requests)
10. [HTTPS and Basic Security](#10-https-and-basic-security)
11. [Practical Observations](#11-practical-observations)
12. [QA Checklist](#12-qa-checklist)
13. [Scope and Limitations of the Checks](#13-scope-and-limitations-of-the-checks)

---

## 1. HTTP Requests and Responses

In a typical web scenario, the browser sends an HTTP request, the server processes it, and the server returns an HTTP response.

```text
Browser (client)
       |
       | HTTP request
       v
     Server
       |
       | HTTP response
       v
Browser (client)
```

### Request

- **Method** — the type of operation requested, such as `GET` or `POST`.
- **URL** — the address of the requested resource.
- **Headers** — request metadata.
- **Body** — optional data sent by the client.

### Response

- **Status code** — the result at the HTTP level.
- **Headers** — response metadata and instructions for the client.
- **Body** — the response content; it may be absent, for example with `204 No Content`.

HTTP, URL, and JSON are different concepts:

- **HTTP** — the communication protocol.
- **URL** — the address of a resource.
- **JSON** — a data representation format commonly used in HTTP message bodies.

### Inspecting requests in Chrome DevTools

1. Open DevTools → **Network**.
2. Perform an action or load a resource.
3. Select the relevant request in the list.
4. In **Headers**, inspect Request URL, Request Method, Status Code, and headers.
5. In **Payload**, inspect the data sent by the client, if any.
6. In **Response** or **Preview**, inspect the response body.

When investigating navigation and redirects, enable **Preserve log** and clear Network filters so the initial request and subsequent requests remain visible in the same sequence.

---

## 2. URLs and Parameters

Example URL:

```text
https://example.com:443/products/42?color=blue&page=2#details
```

| Element | Example | Purpose |
|---|---|---|
| Scheme | `https` | The access scheme/protocol |
| Host | `example.com` | The hostname |
| Port | `443` | The connection port; `443` is the default for HTTPS and `80` for HTTP |
| Path | `/products/42` | The path to the resource |
| Query string | `?color=blue&page=2` | The parameter string in the URL |
| Query parameters | `color=blue`, `page=2` | Parameter names and values |
| Fragment | `#details` | A client-side navigation fragment; it is generally not sent to the server as part of the HTTP request target |

### Path Parameters vs. Query Parameters

- `/users/123` — `123` is in the path and may be a path parameter if the route is defined as `/users/{id}`.
- `/users?id=123` — `id=123` is a query parameter.
- `/orders/456?status=pending` — the order identifier is in the path, while the status filter is in the query string.

When a URL is passed inside another URL as a parameter value, special characters may be encoded using **percent-encoding**. For example, `%3A` represents `:`, `%2F` represents `/`, `%3F` represents `?`, and `%3D` represents `=`.

**QA checks:** verify path and query parameters, behaviour when parameters are missing or invalid, preservation of filters during navigation, and the absence of sensitive data in URLs.

---

## 3. HTTP Methods

The method defines the semantics of a request. The actual behaviour of a specific endpoint must still match the API contract and application requirements.

| Method | Typical purpose | Safe | Idempotent | QA focus |
|---|---|---:|---:|---|
| `GET` | Retrieve a resource or data | Yes | Yes | Check the returned data and the absence of unintended state changes |
| `POST` | Create a resource or perform an operation | No | Not guaranteed | Verify the operation result and the risk of duplicate actions on retries |
| `PUT` | Create or replace a resource representation at the target URI | No | Yes | Check replacement behaviour against the endpoint contract and the result of repeating the request |
| `PATCH` | Partially modify a resource | No | Depends on the operation | Check the changed fields and ensure other data remains intact |
| `DELETE` | Delete a resource | No | Yes, by semantics | Check the final state after deletion and the expected behaviour of a repeated request |
| `HEAD` | Retrieve headers without a response body | Yes | Yes | Check resource availability and metadata |
| `OPTIONS` | Retrieve information about communication options/methods | Yes | Yes | Can be used in CORS preflight scenarios, among other cases |

**Safe** means that the method is not intended to change resource state. It does not guarantee a successful response.

**Idempotent** means that repeating the same request has the same intended effect on the resource state. The status code and response body may still differ between responses.

### PUT vs. PATCH

- `PUT` is generally used to set or replace a resource representation according to the endpoint contract.
- `PATCH` is used for a partial resource update.

For example, a PATCH request may change only the `role` field while leaving `name` and `email` unchanged. A PUT request typically sends a full resource representation when that is what the API contract requires.

### Request examples

**PUT** to a test echo endpoint:

```javascript
fetch("https://httpbin.org/put", {
  method: "PUT",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Alex", role: "QA" })
})
  .then(async response => ({
    status: response.status,
    body: await response.json()
  }))
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

**DELETE** to a test echo endpoint:

```javascript
fetch("https://httpbin.org/delete", { method: "DELETE" })
  .then(async response => ({
    status: response.status,
    body: await response.json()
  }))
  .then(result => console.log(result))
  .catch(error => console.error(error));
```

GET, POST, PATCH, PUT, and DELETE requests were sent to `httpbin.org` test endpoints. PUT and DELETE returned status `200`. These requests verified that the methods were sent to the test service; they did not change real business resources.

---

## 4. Request Body and Response Body

The **request body** contains data sent by the client. The **response body** contains data returned by the server. Whether a body is present and how it is formatted depend on the method and endpoint contract.

### JSON — `application/json`

Example request body:

```json
{
  "name": "Alex",
  "role": "QA"
}
```

Header:

```http
Content-Type: application/json
```

In DevTools, request data is available under **Payload** (often in the Request Payload section), while response content is available under **Response** or **Preview**.

A POST request to `https://httpbin.org/post` sent JSON containing the `name` and `role` fields. The service returned `200`, and the Response Body reflected the submitted fields. Similarly, a PATCH request to `https://httpbin.org/patch` with `{"role":"Developer"}` returned the submitted data.

### HTML Forms — `application/x-www-form-urlencoded`

An HTML form was submitted to `https://httpbin.org/forms/post`. The following were observed in Network:

- Request Method: `POST`;
- Status Code: `200`;
- Request `Content-Type`: `application/x-www-form-urlencoded`;
- submitted form fields reflected in the response;
- the `topping` field appeared more than once, with values such as `bacon` and `cheese`.

The format may look like this:

```text
custname=Alex&size=medium&topping=bacon&topping=cheese
```

This illustrates the format and is not a complete copy of all submitted data. Values containing special characters are encoded; the same field name may appear more than once when the form supports multiple values.

**QA checks:** field names, required fields, default values, repeated parameters, encoding of special characters, and correct handling of form data by the server.

### File Upload — `multipart/form-data`

A JPG file was selected on `https://the-internet.herokuapp.com/upload`. The following kind of header was observed in the Request Headers of the `upload` request:

```http
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary...
```

The `boundary` separates the parts of the multipart body. Such a request can contain files and text fields, and each part has its own headers. The browser generates the boundary value, so it may differ between requests.

During this check, a request named `data:image/jpeg...` with status `200 (from memory cache)` turned out to be an image load, not the file upload. The separate `upload` request was selected for analysis.

An attempt to upload a TXT file was rejected by the page; the successful observed scenario used a JPG. The reason for the TXT rejection was not investigated separately.

**Upload QA checks:** permitted file types and sizes, file name, behaviour for unsupported formats, server response, and the difference between the upload request and a subsequent browser image request.

---

## 5. HTTP Headers

Headers contain metadata for an HTTP message. **Request Headers** are sent by the client to the server, while **Response Headers** are sent by the server to the client.

| Header | Where it is used | Purpose | QA focus |
|---|---|---|---|
| `Accept` | Request | Preferred response media types | Verify response format negotiation |
| `Content-Type` | Request / Response | Type of content in the corresponding body | Check that the body matches the declared format |
| `Authorization` | Request | Authentication credentials, such as Bearer credentials | Check requests without credentials, with invalid tokens, and with appropriate permissions |
| `Cookie` | Request | Cookies sent by the browser | Check session transmission and expected user state |
| `Set-Cookie` | Response | Instructs the browser to store or update a cookie | Check cookie attributes and whether the browser sends it later |
| `Cache-Control` | Request / Response | Caching directives | Investigate resource freshness and cache behaviour |
| `ETag` | Usually Response | Validator for a representation version | Check conditional requests and representation versions |
| `If-None-Match` | Request | Condition based on a previously received ETag | Investigate `304 Not Modified` scenarios |
| `Last-Modified` | Response | Time of the last representation change | May be used for conditional requests |
| `If-Modified-Since` | Request | Condition based on a modification date | Check conditional requests based on time |
| `Location` | Response, including redirects and some creation responses | Target URI, depending on the scenario | Verify the destination and the absence of redirect loops |
| `Content-Length` | Request / Response, where applicable | Content length in octets | May help analyse body transmission; it can be absent in some transfer modes |
| `Server` | Response, if provided | Information about server software | Supporting diagnostic information; it is not always present |
| `Allow` | For example, a `405` response | Methods allowed for the resource | Compare supported methods with the endpoint contract |
| `Retry-After` | For example, `429` or `503` responses | Suggests when a request may be retried | Check client behaviour during rate limiting or temporary unavailability |

Headers may be absent or may vary depending on the application and intermediary infrastructure (CDN, proxy, or gateway). The absence of an optional header alone is not proof of a defect.

### Observations in DevTools

- `Content-Type: text/javascript; charset=utf-8` was observed on the JavaScript resource `s.js`.
- `Cache-Control: max-age=60` was confirmed on a test endpoint.
- `Content-Type: multipart/form-data; boundary=...` was observed on the JPG upload request.
- `Content-Type: application/json` was used for JSON requests.
- `Set-Cookie`, `Cookie`, `ETag`, and `If-None-Match` were checked in separate requests (see the relevant sections below).
- Specific values for `Content-Length` and `Server` were not recorded in the practical notes.

### Accept

The request for the main document at `https://example.com/` contained an `Accept` header including `text/html`, `application/xhtml+xml`, `application/xml`, `image/avif`, `image/webp`, and `*/*`, with `q` preference values.

`Accept` communicates the client's preferences for response media types. It is not the server address and does not guarantee that the server will convert its response to another format.

### Authorization

A test request to `https://httpbin.org/anything` included the following demonstration headers:

```http
Authorization: Bearer demo-token
Accept: application/json
```

The header was visible in Request Headers in DevTools. This confirms that the specified value was sent, but it does not validate the token or demonstrate real authorization: `httpbin.org` did not check access permissions in this experiment.

---

## 6. Cookies in HTTP

Two HTTP headers must be distinguished:

- **`Set-Cookie`** — the server tells the browser which cookie to store or update.
- **`Cookie`** — the browser sends stored cookies in matching requests.

### Practical Scenario

1. The endpoint `https://httpbin.org/response-headers?Set-Cookie=qa_demo%3Dtest123` was used to inspect `Set-Cookie`.
2. `Set-Cookie` was found in Response Headers.
3. The URL `https://httpbin.org/cookies` was opened afterwards.
4. Request Headers showed `Cookie: qa_demo=test123`.
5. The JSON response from `/cookies` contained `qa_demo: test123`.

This experiment demonstrated cookie transfer from a server response to a subsequent request. The initial attempt with `/cookies/set` did not make the original `Set-Cookie` response easy to identify in the selected Network row, so the separate `/response-headers` endpoint was used.

In the authentication exercise on `the-internet.herokuapp.com/login`, the `rack.session` cookie and a `Set-Cookie` header in authentication responses were also observed. The real session-cookie value is not included in this documentation.

### Cookie and Session QA Considerations

- The presence of a cookie in the browser does not, by itself, prove that the server considers the user authenticated.
- Cookie applicability depends on Domain, Path, Expires/Max-Age, Secure, and SameSite.
- `HttpOnly` prevents access to a cookie through the ordinary JavaScript API `document.cookie`, but it does not stop the browser from sending the cookie with matching HTTP requests.
- Real session cookies, tokens, and passwords should not be published in repositories, screenshots, or logs.

---

## 7. Status Codes

A status code describes the result at the HTTP level. It should be evaluated together with the endpoint contract, response body, and the application's actual behaviour.

### Status Code Classes

| Class | Meaning |
|---|---|
| `1xx` | Informational responses |
| `2xx` | The request was successfully processed |
| `3xx` | Redirection or conditional-cache handling |
| `4xx` | A problem with the request or a refusal to process it |
| `5xx` | An error in the server or intermediary infrastructure |

### Common Status Codes for Manual QA

| Code | Meaning | What to check |
|---|---|---|
| `200 OK` | Successful response | Response content and the actual result of the operation |
| `201 Created` | A resource was created | ID, `Location` if present, response body, and whether a subsequent request can retrieve the resource |
| `204 No Content` | Success with no response body | That the body is absent and the client handles the response correctly |
| `301 Moved Permanently` | Permanent redirect | Target URL and navigation from the previous address |
| `302 Found` | Temporary redirect | `Location`, final URL, and whether behaviour matches the scenario |
| `304 Not Modified` | The representation has not changed relative to a validator | Conditional request and correct use of the stored representation |
| `400 Bad Request` | Invalid request | Parameters, format, request structure, and error message |
| `401 Unauthorized` | Suitable authentication is missing or invalid | Missing/invalid credentials and client behaviour |
| `403 Forbidden` | Access is denied | Roles, permissions, and access to other users' resources |
| `404 Not Found` | The resource was not found, or its existence is not disclosed | URL, ID, environment, resource state, and API requirements |
| `405 Method Not Allowed` | The method is not allowed for the resource | Method correctness and `Allow`, if present |
| `409 Conflict` | Conflict with the current resource state | Duplicates, versions, repeated operations, and state constraints |
| `422 Unprocessable Content` | The content failed semantic validation | Field validation and error structure; an API may use `400` for similar cases |
| `429 Too Many Requests` | Request rate limit reached | Rate limiting, client message, and `Retry-After` if present |
| `500 Internal Server Error` | Internal server error | Reproducibility, request/correlation ID, and side effects |
| `502 Bad Gateway` | A gateway received an invalid response from an upstream server | Gateway/proxy and dependent-service availability |
| `503 Service Unavailable` | The service is temporarily unavailable | Availability, overload/maintenance, and `Retry-After` if present |

### Practical Observations

- Most test requests to echo endpoints returned `200`.
- The endpoint `https://httpbingo.org/status/201` returned `201`.
- The scenario `POST /users → 201`, followed by `GET /users/123 → 404`, was discussed as a QA investigation case. A `201` response alone does not prove that a resource persisted.
- The other codes in the table are reference material; not every code was tested with a separate request in these notes.

### Investigating `201`, Then `404`

1. Check the POST request: endpoint, payload, status, response body, returned ID, and `Location` if present.
2. Check the GET request: exact URL, ID, method, host/environment, and Authorization.
3. Repeat the scenario and establish whether the issue occurs immediately or after some delay.
4. If access is available, check logs and whether the resource was persisted in storage.
5. Compare the findings with the API contract. Do not select a root cause without supporting evidence.

---

## 8. Redirects

An HTTP redirect tells the client to navigate to another address. A server-side redirect usually contains a 3xx status code and a `Location` header.

Example:

```http
GET /old-page HTTP/1.1
Host: shop.example
```

```http
HTTP/1.1 302 Found
Location: https://shop.example/new-page
```

After receiving this response, the browser usually sends a subsequent request to the target URL. The final resource may return, for example, `200 OK`.

### Common Redirect Status Codes

- `301 Moved Permanently` — permanent redirect.
- `302 Found` — temporary redirect.
- `303 See Other` — the subsequent request is generally made using GET.
- `307 Temporary Redirect` — temporary redirect that preserves the method and request body.
- `308 Permanent Redirect` — permanent redirect that preserves the method and request body.

### Server Redirect vs. HSTS

When `http://www.katipunkt.de/` was opened in DevTools, Chrome showed `307 Internal Redirect`, `Location: https://www.katipunkt.de/`, and `Non-Authoritative-Reason: HSTS`. The subsequent HTTPS request returned `200`.

This was an internal Chrome redirect caused by HSTS, not a confirmed server-side HTTP response with status `307`.

### Redirect QA Checks

- Enable Preserve log and clear Network filters.
- Find the original request, not only the final URL.
- Distinguish a network 3xx response from a browser `Internal Redirect`.
- Check `Location`, the final address, and the absence of redirect loops.
- Verify the expected behaviour of the method, body, and parameters after the redirect.
- Check for unexpected redirects from HTTPS to HTTP.

---

## 9. Caching and Conditional Requests

HTTP caching allows a previously received representation to be reused while it remains suitable. The presence of a caching header alone does not prove that a particular request was served from cache.

### Cache-Control

The response header below was confirmed on `https://httpbin.org/response-headers?Cache-Control=max-age%3D60`:

```http
Cache-Control: max-age=60
```

`max-age=60` indicates that the response may be considered fresh for 60 seconds after it is received, subject to the other HTTP caching rules. Actual cache use also depends on the cache key, other directives, cache state, and DevTools settings.

### ETag and If-None-Match

`ETag` is a validator for a representation version. The client can send it in a conditional request using `If-None-Match`.

Example request:

```http
GET /resource HTTP/1.1
If-None-Match: "version-123"
```

If the representation has not changed relative to the validator, the server may return:

```http
HTTP/1.1 304 Not Modified
ETag: "version-123"
```

With `304`, a new regular response body is not sent; the browser can use the stored representation.

### ETag Practice

The endpoint below was used:

`https://httpbin.org/etag/qa-demo-123`

The following were observed in Network:

- Status Code: `304`;
- Response Header: `ETag: qa-demo-123`;
- Request Header: `If-None-Match: qa-demo-123`.

This demonstrated a conditional request using ETag in DevTools.

### Last-Modified and If-Modified-Since

- `Last-Modified` indicates when the representation was last changed.
- `If-Modified-Since` allows a conditional request based on that date.

These headers were reviewed conceptually; a separate experiment with them was not recorded.

### Caching QA Checks

- Check `Cache-Control` and relevant directives such as `no-cache`, `no-store`, `private`, and `public`.
- Check `ETag`/`If-None-Match` or `Last-Modified`/`If-Modified-Since`, where applicable.
- Do not confuse `304` with a regular `200`: a new response body is generally not sent with `304`.
- Verify that updated data does not remain incorrectly stale.
- Keep in mind that **Disable cache** in DevTools changes the conditions of the experiment.

---

## 10. HTTPS and Basic Security

### HTTP vs. HTTPS

- HTTP alone does not encrypt data in transit.
- HTTPS is HTTP over TLS. TLS protects data in transit from being read or modified without detection and allows the browser to verify the server's identity using its certificate and hostname.
- A secure-connection indicator does not mean a website is trustworthy, free of vulnerabilities, or that its business logic is correct.

For `https://httpbin.org/get`, Chrome displayed **Connection is secure**. This confirms the browser's secure-connection indicator for the observed connection; it is not a security audit of the website.

### Authentication vs. Authorization

- **Authentication** verifies who the user or client is.
- **Authorization** determines which actions that user or client is allowed to perform.

For example, an authenticated user may receive `403 Forbidden` when attempting to delete another user's order if they do not have the required permissions.

### Bearer Token

A Bearer token is often sent in this header:

```http
Authorization: Bearer <token>
```

The practical request used the demonstration value `Bearer demo-token`. The presence of the header was checked in Request Headers, but the token's validity and server-side permission checks were not tested.

### Basic Security QA Checks

- Check whether secrets are exposed in URLs or public logs.
- Check application behaviour without Authorization and with an invalid token.
- Distinguish missing authentication from insufficient permissions.
- Check whether a user can access or modify another user's resource by changing its ID.
- Verify that HTTPS is used where credentials and session information are transmitted.
- Check whether session cookies are handled correctly.

OAuth, JWT, and low-level TLS details are outside the scope of this basic overview.

---

## 11. Practical Observations

| Scenario | Observation in DevTools | Result |
|---|---|---|
| Main `example.com` document | `GET https://example.com/`, Type `document`, Status `304` | The main document request was found; caching for this specific request was not investigated separately |
| JavaScript resource `s.js` | `Content-Type: text/javascript; charset=utf-8` | The script content-type header was inspected |
| GET `httpbin.org/get?foo=bar` | `foo=bar` was reflected in the JSON response under `args` | A query parameter was observed in the test service response |
| POST with JSON | `POST /post`, payload with `name` and `role`, status `200`, `application/json` | Submitted data was reflected in the response body |
| PATCH with JSON | `PATCH /patch`, payload `{"role":"Developer"}`, status `200` | The test service reflected the submitted field |
| PUT | `PUT /put` with JSON, status `200` | The method and body were sent to the test endpoint |
| DELETE | `DELETE /delete`, status `200` | The method was checked on the test endpoint; no real resource was deleted |
| Authorization | `Bearer demo-token` in Request Headers | Header transmission was observed; token validity was not checked |
| Cookie / Set-Cookie | `Set-Cookie` in a response, followed by `Cookie: qa_demo=test123` in a request to `/cookies` | Cookie transfer between sequential requests was confirmed |
| Cache-Control | `Cache-Control: max-age=60` | The header was observed in Response Headers |
| Conditional request | `If-None-Match`, `ETag`, and status `304` on `/etag/qa-demo-123` | Conditional caching behaviour was observed |
| Status `201` | `https://httpbingo.org/status/201` | The test endpoint returned `201`; no real resource was created |
| HTML form | `POST /forms/post`, status `200`, `application/x-www-form-urlencoded` | Form fields were reflected in the response; repeated `topping` values were also observed |
| File upload | JPG upload to `/upload` on `the-internet.herokuapp.com`, `multipart/form-data; boundary=...` | The upload request header was observed |
| HTTPS | Chrome displayed `Connection is secure` for `https://httpbin.org/get` | A secure connection was indicated in the browser UI |
| HSTS | `307 Internal Redirect`, `Location` to HTTPS, `Non-Authoritative-Reason: HSTS`, followed by `200` | Chrome's internal redirect was observed, not a confirmed server-side `307` |

---

## 12. QA Checklist

### Request

- [ ] Do the Request URL, method, path, and query parameters match the expected scenario?
- [ ] Are Request Headers (`Accept`, `Content-Type`, `Authorization`, `Cookie`) correct when required?
- [ ] Does the Request Payload match the expected schema and format?
- [ ] Is any unnecessary personal data or secret being transmitted?

### Response

- [ ] Does the Status Code match the endpoint contract?
- [ ] Are Response Headers and `Content-Type` correct?
- [ ] Does the Response Body match the expected structure and data?
- [ ] Has the business result been verified separately when a `2xx` response alone is insufficient?
- [ ] If there is an error, have the message, response format, and possible side effects been checked?

### Methods and Data

- [ ] **POST:** Was the expected resource created, and were duplicate resources avoided?
- [ ] **PUT:** Was replacement performed according to the contract?
- [ ] **PATCH:** Were the intended fields changed while the other fields remained intact?
- [ ] **DELETE:** Does the final state match expectations?
- [ ] **HTML forms:** Are the encoding format, fields, and repeated parameters correct?
- [ ] **Upload:** Are file type, size, and content handled correctly?

### Redirects and Cache

- [ ] Is Preserve log enabled, and has the original request been found?
- [ ] Has a server-side redirect been distinguished from a browser `Internal Redirect`?
- [ ] Are `Location` and the final URL correct?
- [ ] For `304`, have the conditional request headers and validators been checked?
- [ ] Does the UI avoid displaying stale data after a resource is updated?

### Authentication and Authorization

- [ ] Have requests without a token and with an invalid token been checked, where relevant to the scenario?
- [ ] Have roles and access to resources belonging to different users been checked?
- [ ] Have real tokens and personal data been kept out of public files and screenshots?

---

## 13. Scope and Limitations of the Checks

The examples in this document demonstrate HTTP requests, headers, bodies, and responses in a test environment. They are not evidence that production business logic was tested.

The following limits of the observations are explicitly recorded:

- The full server-side redirect chain `302 → Location → next request` was not reliably captured in the saved Network observations. An internal Chrome HSTS redirect was observed, which is a different mechanism.
- `Last-Modified` / `If-Modified-Since` were reviewed conceptually, but a separate experiment was not recorded.
- Specific values for `Content-Length` and `Server` were not preserved in the practical notes.
- Not every status code in the reference table was checked with a separate request; a practical example was obtained for `201`.
- The demonstration Bearer token was checked at the header level, not through real server-side authentication or authorization.

These limitations distinguish observed results from general protocol knowledge and help avoid unsupported conclusions.

---

## Summary

HTTP defines how clients and servers exchange requests and responses. For Manual QA, inspecting Network in Chrome DevTools helps analyse URLs, methods, headers, payloads, status codes, response bodies, cookies, redirects, and caching.

The practical examples documented here cover GET, POST, PATCH, PUT, and DELETE; JSON and HTML forms; file upload; `Cookie` / `Set-Cookie`; `Authorization`, `Cache-Control`, `ETag`, and `If-None-Match`; responses with status `200`, `201`, and `304`; and Chrome's internal HSTS redirect.
