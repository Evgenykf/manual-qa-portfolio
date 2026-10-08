# Cookies — DevTools Practice

> **Status:** ✅ Theory + basic practical inspection completed  
> **Practice:** Chrome DevTools  
> **Test site:** `the-internet.herokuapp.com/login`

## 1. What is a cookie?

A **cookie** is a small piece of data stored by the browser and associated with a website.

Basic structure:

```text
Name=Value
```

A cookie can also contain attributes that control where, when, and how it is sent.

### Main cookie attributes

| Attribute | What it controls |
|---|---|
| `Name` | Cookie name |
| `Value` | Stored cookie value |
| `Domain` | Which domain(s) can use the cookie |
| `Path` | Which URL paths the cookie applies to |
| `Expires` / `Max-Age` | How long the cookie remains valid |
| `HttpOnly` | Prevents normal JavaScript access through `document.cookie` |
| `Secure` | Sends the cookie only over HTTPS |
| `SameSite` | Controls cross-site cookie sending and helps reduce CSRF risk |

---

## 2. Cookies in authentication

A common login flow looks like this:

```text
Username + password
        ↓
POST /authenticate
        ↓
Server validates credentials
        ↓
Response: Set-Cookie
        ↓
Browser stores session cookie
        ↓
Later requests: Cookie: ...
        ↓
Server uses session information
```

### `Set-Cookie` vs `Cookie`

| Header | Direction | Purpose |
|---|---|---|
| `Set-Cookie` | Server → Browser | Tells the browser to create or update a cookie |
| `Cookie` | Browser → Server | Sends stored cookies with a matching request |

---

## 3. `rack.session` observed in practice

During the DevTools exercise, a cookie named `rack.session` was observed.

Its value looked like an opaque session identifier.

The real value is **not documented here**, because session values can act as credentials and should not be published.

Safe documentation example:

```text
rack.session=<REDACTED>
```

---

## 4. Chrome DevTools practice

### Application → Cookies

Opened:

```text
Application
→ Storage
→ Cookies
```

Observed the cookie:

```text
rack.session
```

Inspected its properties, including:

- Name
- Value
- Domain
- Path
- Expires / Max-Age
- HttpOnly
- Secure
- SameSite

### Network → authenticate

Enabled **Keep log** and inspected the authentication request:

```text
Network
→ authenticate
→ Headers
→ Response Headers
```

Observed a `Set-Cookie` header containing the session cookie.

### Network → secure

Inspected a later request and compared the cookie information with what was visible in **Application → Cookies**.

This connected the browser's stored cookie with the HTTP request/response flow.

---

## 5. What `HttpOnly` means

`HttpOnly` prevents normal page JavaScript from reading the cookie through `document.cookie`.

The browser can still send the cookie to the server when the request matches the cookie's rules.

**Important:**

```text
HttpOnly ≠ "the server cannot receive the cookie"
```

It mainly limits client-side JavaScript access.

---

## 6. Important observation from the experiment

The `rack.session` cookie did not visibly change every time login/logout/browser restart was tested.

Therefore, this is **not** a reliable rule:

> A session cookie must always change after every login or logout.

For QA investigation, check the actual browser and HTTP behaviour instead of assuming what the cookie must do.

Useful checks:

1. Cookie attributes
2. `Set-Cookie` in the response
3. `Cookie` in the request
4. Behaviour before login
5. Behaviour after login
6. Behaviour after logout

The presence of a cookie in DevTools does not, by itself, prove that the server currently considers the session authenticated.

---

## 7. Cookie vs localStorage vs sessionStorage

| Storage | Sent automatically with matching HTTP requests | JavaScript access | Typical persistence |
|---|---|---|---|
| Cookie | Yes | Yes, unless `HttpOnly` | Depends on cookie lifetime |
| `localStorage` | No | Yes | Usually survives browser restart |
| `sessionStorage` | No | Yes | Usually tied to the page/tab session |

---

## 8. What I learned

The practical exercise connected the browser UI with the HTTP authentication flow:

```text
Login
  ↓
authenticate
  ↓
Response Headers
  ↓
Set-Cookie: rack.session=...
  ↓
Browser stores cookie
  ↓
Application → Cookies
  ↓
rack.session
  ↓
Later HTTP requests can include the cookie
```

The main takeaway is that cookies are part of the browser–server HTTP interaction, not just values visible in the browser's storage panel.

---

## 9. QA checklist

When investigating cookies in DevTools, check:

- [ ] Name
- [ ] Value — never publish real secrets
- [ ] Domain
- [ ] Path
- [ ] Expires / Max-Age
- [ ] HttpOnly
- [ ] Secure
- [ ] SameSite
- [ ] `Set-Cookie` in the response
- [ ] `Cookie` in the request
- [ ] Behaviour before login
- [ ] Behaviour after login
- [ ] Behaviour after logout

---

## 10. Security note

Never publish real values of:

- session cookies
- authentication tokens
- passwords
- API keys
- authorization headers
- other secrets

Use placeholders in public GitHub documentation:

```text
rack.session=<REDACTED>
```

---

## 11. Next topic

**Cookies / Storage → HTTP**

Next file:

```text
http.md
```

The HTTP block will cover:

- Request and Response
- HTTP methods
- URL
- Headers
- Body
- Query Parameters
- Path Parameters
- Status Codes
- `Content-Type`
- Authentication
- Cookies inside HTTP