# HTTP — Web Basics & DevTools Practice

> **Purpose:** personal learning notes for Web Basics and Manual QA  
> **Practice tools:** Chrome DevTools → Network and Console  
> **Test service:** `httpbin.org`  
> **Status:** foundations covered; Request / Response Headers are still in progress

This document records concepts and observations that were discussed or checked during practice. Items marked as not completed have not been treated as verified results.

## Current progress

| Topic | Status | Notes |
|---|---|---|
| HTTP, Client, Server, Request, Response | Basics covered | Core request–response model understood |
| URL and parameters | Basics covered | Scheme, host, port, path, query string, and path/query parameters discussed; an independent Network check can reinforce it |
| HTTP methods | Basics covered | GET, POST, PUT, PATCH, DELETE, safe methods, and idempotency discussed; PUT and DELETE have not been tested hands-on |
| Request Body and Response Body | Practised with JSON | POST and PATCH requests sent to `httpbin.org` |
| Request / Response Headers | In progress | `Accept`, `Content-Type`, and a demo `Authorization` header observed |
| Form Data and file uploads | Not covered systematically | `application/x-www-form-urlencoded` and `multipart/form-data` remain to be studied |
| Status codes | Not covered systematically | Need a QA-focused review of common status codes |
| Redirects and caching | Not completed | Redirect chain was not reliably verified; caching behaviour for the observed 304 response was not fully investigated |
| HTTPS and HTTP security basics | Not covered systematically | Still on the learning plan |

## 1. HTTP, Client, Server, Request, and Response

The basic interaction model is:

```text
Browser (Client)
      |
      | HTTP Request
      v
    Server
      |
      | HTTP Response
      v
Browser (Client)
```

- **Client** — the side that initiates a request. In our practice, this was Chrome.
- **Server** — the side that receives the request and generates a response.
- **Request** — a message sent by the client to the server.
- **Response** — a message returned by the server to the client.
- **HTTP** — the protocol used for the exchange.
- **URL** — the address of the resource being requested.
- **JSON** — a format for representing data; it is not a protocol or an address.

A URL does not, by itself, establish the network connection. DNS can resolve a domain name to an IP address, while the relevant network connection allows the client and server to exchange data.

## 2. URL and parameters

Example URL:

```text
https://api.example.com:8443/users/123?page=2&limit=10
```

| Element | Example | Meaning |
|---|---|---|
| Scheme | `https` | The URL scheme |
| Host | `api.example.com` | The host name |
| Port | `8443` | An explicitly specified port |
| Path | `/users/123` | The path to the resource |
| Query string | `?page=2&limit=10` | The query part of the URL |
| Query parameters | `page=2`, `limit=10` | Parameter names and values |

For standard HTTP and HTTPS URLs, the default ports are 80 and 443 respectively when no port is written explicitly:

- `http://example.com` → default port `80`
- `https://example.com` → default port `443`
- `https://example.com:8443` → explicitly uses port `8443`

A non-standard port does not mean a server is local. Whether a host is local depends on the host/address, not merely on the port number.

### Path parameters vs. query parameters

```text
/users/123
```

Here, `123` is a path segment. It acts as a **path parameter** if the API route defines that segment as a variable, such as `/users/{id}`.

```text
/users?id=123
```

Here, `id=123` is a **query parameter**.

Another example:

```text
/orders/456?status=pending
```

- Path: `/orders/456`
- Possible path parameter: `456` (depending on the route definition)
- Query parameter: `status=pending`

### Practice observation

We opened this test URL in the browser and inspected it in Network:

```text
https://httpbin.org/anything/users/123?page=2&limit=10
```

The initial breakdown needed correction. The final interpretation was:

- No port was explicitly written; HTTPS normally uses port `443` by default.
- Path: `/anything/users/123`
- Query parameters: `page=2` and `limit=10`
- `123` may act as a path parameter if the route defines it that way.

The concepts have been discussed, but a complete independent breakdown of every URL part in Network can be used as a later revision exercise.

## 3. HTTP methods

### GET and POST

- **GET** is intended to retrieve a representation of a resource without requesting a change to application state.
- **POST** submits data for processing and is often used to create a resource, but it is not limited to creation.

A method alone does not guarantee the exact outcome; the endpoint and API contract matter.

### PUT and PATCH

Example resource:

```json
{
  "id": 123,
  "name": "Alex",
  "role": "QA"
}
```

Example PUT request:

```http
PUT /users/123
Content-Type: application/json
```

```json
{
  "name": "Alex",
  "role": "Developer"
}
```

**PUT** is commonly used to create or replace the state/representation of a resource at a specific URI. Whether omitted fields are removed or defaulted depends on the API contract.

Example PATCH request:

```http
PATCH /users/123
Content-Type: application/json
```

```json
{
  "role": "Developer"
}
```

**PATCH** is commonly used for a partial modification of a resource.

### PATCH practice with `httpbin.org`

We sent this request from the browser Console:

```javascript
fetch("https://httpbin.org/patch", {
  method: "PATCH",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    role: "Developer"
  })
})
  .then(response => response.json())
  .then(data => console.log(data));
```

In Network, we observed:

- Request Method: `PATCH`
- Request Payload: `{"role":"Developer"}`
- Status Code: `200`
- Response Body: JSON containing `role: "Developer"`

This was a real request/response observation, but `httpbin.org` only reflected the submitted data. We did not update a real user or database.

### Safe and idempotent methods

- A **safe** method is defined as read-only in terms of the requested application semantics.
- An **idempotent** method has the same intended effect on server state when the same request is repeated. The response status or body does not have to be identical every time.

| Method | Safe? | Idempotent? | Notes |
|---|---|---|---|
| GET | Yes | Yes | Intended to retrieve a representation |
| POST | No | Usually not | Repetition may create/process additional effects |
| PUT | No | Yes | Repeatedly setting the same intended resource state should have the same effect |
| PATCH | No | Not guaranteed | Depends on the patch operation and API semantics |
| DELETE | No | Yes | Repeating it can return a different status, while the intended end state may remain the same |

We discussed why a second DELETE request might return `404` after the first returned `200`: idempotency concerns the intended effect on the resource state, not a guarantee of identical status codes. We also noted that repeating a fixed assignment can be idempotent, while repeating an increment operation may not be.

**Hands-on coverage:** GET, POST, and PATCH were observed in DevTools. PUT and DELETE were discussed theoretically and still need hands-on practice.

## 4. Request Body and Response Body

### POST practice with JSON

We sent this POST request to `httpbin.org`:

```javascript
fetch("https://httpbin.org/post", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Alex",
    role: "QA"
  })
})
  .then(response => response.json())
  .then(data => console.log(data));
```

In `Network → post → Payload`, the Request Body was:

```json
{
  "name": "Alex",
  "role": "QA"
}
```

We then inspected the response and observed that the service returned JSON containing the submitted data. The response status was `200`, and its `Content-Type` was `application/json`.

### Key distinctions

- **Request Body** — data sent by the client.
- **Response Body** — data returned by the server.
- **Content-Type** — describes the media type of the message body.
- **JSON** — a data format that can be used in a request body or response body.

A successful HTTP status does not, by itself, prove that the intended business operation happened correctly. In this exercise, `httpbin.org` reflected the request; it did not create a real user.

**Not covered yet:** HTML form data, `application/x-www-form-urlencoded`, and `multipart/form-data` (including file uploads).

## 5. Request Headers and Response Headers

This section is in progress.

- **Request Headers** are sent by the client.
- **Response Headers** are returned by the server.
- Headers contain metadata about the HTTP message. Business data is commonly carried in the request or response body instead.

### 5.1. `Accept`

On the main `example.com` document request, we observed a header like this:

```http
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
```

Some values discussed:

| Value | Meaning |
|---|---|
| `text/html` | HTML media type |
| `application/xhtml+xml` | XHTML media type |
| `application/xml` | XML media type |
| `image/avif` | AVIF image media type |
| `image/webp` | WebP image media type |
| `*/*` | Any media type |
| `q=0.9` | A relative preference weight of `0.9` |

`Accept` communicates the client's response-format preferences. It is not a server address and does not guarantee that the server will convert a response from one format to another.

### 5.2. `Content-Type`

On a JavaScript resource named `s.js`, we observed:

```http
Content-Type: text/javascript; charset=utf-8
```

This identified the response content as JavaScript text using UTF-8 character encoding.

On the `httpbin.org` POST response, we observed:

```http
Content-Type: application/json
```

`Content-Type` describes the body of the HTTP message in which it appears. A request can have its own `Content-Type`, and a response can have a different one.

The DevTools message `Failed to load response data` appeared during inspection of the script request. That UI message alone does not prove that the server returned an HTTP error.

### 5.3. `Authorization`

We sent a demonstration header to `httpbin.org`:

```javascript
fetch("https://httpbin.org/anything", {
  headers: {
    "Authorization": "Bearer demo-token",
    "Accept": "application/json"
  }
})
  .then(response => response.json())
  .then(data => console.log(data));
```

In Network → Request Headers, we observed:

```http
Authorization: Bearer demo-token
Accept: application/json
```

This proved that the header was sent and visible in DevTools. `demo-token` was a demonstration value, not a real credential. We did not test real authentication, and `httpbin.org` did not validate the token.

### 5.4. Status `304` and cache investigation

On the main `example.com` document request, we observed:

| Field | Observed value |
|---|---|
| Name | `example.com` |
| Status | `304` |
| Type | `document` |
| Request URL | `https://example.com/` |
| Request Method | `GET` |

`304 Not Modified` is associated with cache validation and is not a typical `4xx` or `5xx` error. We did not inspect all relevant conditional request headers for this specific request, so the exact cache-validation mechanism was not established.

### Header progress

| Header | Current state |
|---|---|
| `Accept` | Discussed and observed |
| `Content-Type` | Observed in JavaScript and JSON responses |
| `Authorization` | Sent as a demonstration value and observed in Request Headers |
| `Cookie` | To cover in the HTTP context, building on the completed Cookies topic |
| `Set-Cookie` | To cover in the HTTP context, building on the completed Cookies topic |
| `Cache-Control` | Not covered yet |
| `Location` | Not verified through a complete redirect chain |
| `Content-Length` | Not covered yet |
| `Server` | Not covered yet |

We attempted to inspect a redirect using `https://httpbin.org/redirect/1`, but we did not reliably verify the expected `302 → next request` sequence. This exercise is **not** considered completed.

## 6. Hands-on observations so far

These are the concrete observations from practice:

| Practice | What was observed |
|---|---|
| Opened `example.com` | Found the main document request in Network |
| Inspected the main document | Saw `GET`, Request URL `https://example.com/`, status `304`, and type `document` |
| Inspected `Accept` | Saw the browser's response-format preferences in Request Headers |
| Inspected a JavaScript resource | Saw `Content-Type: text/javascript; charset=utf-8` |
| GET to `httpbin.org/get?foo=bar` | Saw `foo=bar` reflected in the JSON response under `args` |
| POST to `httpbin.org/post` | Sent JSON containing `name: Alex` and `role: QA` |
| Inspected the POST response | Saw status `200`, `Content-Type: application/json`, and the reflected data |
| PATCH to `httpbin.org/patch` | Sent `{"role":"Developer"}` and saw the field reflected in the JSON response with status `200` |
| Authorization header | Sent `Bearer demo-token` and found it in Request Headers |
| Redirect test | Not completed; the redirect sequence was not reliably confirmed |

For the GET query-parameter exercise, we opened:

```text
https://httpbin.org/get?foo=bar
```

The response body contained a JSON structure with `args.foo` set to `bar`. The `headers` object shown **inside the JSON response body** described request headers reflected by the service; it was not the same thing as the actual Response Headers shown in DevTools.

The broader request/response flow we have started to inspect is:

```text
Request
  ↓
Method + URL
  ↓
Request Headers
  ↓
Request Body (when present)
  ↓
Server
  ↓
Response
  ↓
Status Code
  ↓
Response Headers
  ↓
Response Body (when present)
```

## 7. What remains in the HTTP learning plan

| Topic | Remaining work |
|---|---|
| URL and parameters | Independently identify all parts of a full URL in Network as a refresher |
| HTTP methods | Practise PUT and DELETE; reinforce safe and idempotent semantics |
| Headers | Study `Cookie`, `Set-Cookie`, `Cache-Control`, `Location`, `Content-Length`, and `Server` |
| Request Body | Practise Form Data, `application/x-www-form-urlencoded`, and `multipart/form-data` |
| Status Codes | Study the main `2xx`, `3xx`, `4xx`, and `5xx` codes from a Manual QA perspective |
| Redirects and caching | Confirm a redirect chain and study `Location`, conditional requests, and `304` |
| HTTPS and security | Cover HTTPS basics, authentication, and authorization in the HTTP context |

## 8. GitHub documentation notes

This file records what we actually discussed and observed. It does **not** claim that we created a real user, changed a database, validated a genuine token, or successfully verified a redirect chain.

When documenting requests, use placeholders for credentials and secrets. Do not publish real passwords, session cookie values, access tokens, or other sensitive data.

The HTTP section will be considered complete only after the remaining topics have been studied and the relevant practical observations have been checked. Until then, this file should be updated as each part is completed.
