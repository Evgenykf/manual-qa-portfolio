Cookies — DevTools practice

Status

Block: Cookies / Browser Storage
Practice: Chrome DevTools
Test site: the-internet.herokuapp.com/login
Result: basic theory and practical inspection completed.

1. What is a cookie?

A cookie is a small piece of data stored by the browser and associated with a website.

The basic representation is:

Name=Value

A cookie can also have attributes that control where, when, and how it is sent.

Main cookie attributes

Attribute

Meaning

Name

Cookie name

Value

Cookie value

Domain

Domain(s) for which the cookie applies

Path

URL path scope

Expires / Max-Age

Lifetime of the cookie

HttpOnly

Prevents normal JavaScript access through document.cookie

Secure

Sends the cookie only over HTTPS

SameSite

Controls cross-site cookie sending and helps reduce CSRF risk

2. Cookies and authentication

A common authentication flow looks like this:

User enters username/password
        ↓
POST /authenticate
        ↓
Server validates credentials
        ↓
Server returns Set-Cookie
        ↓
Browser stores the cookie
        ↓
Browser sends Cookie on later matching requests
        ↓
Server uses the session information to identify the session

Important distinction:

Set-Cookie is typically sent from server to browser in a response.

Cookie is sent from browser to server in a request.

3. rack.session observed in practice

During DevTools practice, the application used a cookie named:

rack.session

Its value looked like a long opaque session identifier. The value itself is not documented here because session values are credentials/secrets and should not be published to GitHub.

The same cookie was observed during the login/session flow in DevTools.

4. What was checked in Chrome DevTools

Application

Opened:

Application
→ Storage
→ Cookies

Observed:

rack.session

and inspected its properties such as name, value, domain, path, and security-related attributes.

Network

Enabled:

Keep log

This keeps network entries visible across navigation/reload events.

Then inspected the authentication request:

Network
→ authenticate
→ Headers
→ Response Headers

Observed a Set-Cookie response header containing the session cookie.

The cookie included HttpOnly in the observed response.

A later protected request (secure) was also inspected, and the session cookie information was visible in the request/response flow.

5. What HttpOnly means

HttpOnly means normal page JavaScript cannot read the cookie through document.cookie.

The browser can still send the cookie to the server when the request matches the cookie's rules.

So:

HttpOnly ≠ "the server cannot receive the cookie"

Instead, it mainly limits client-side JavaScript access to the cookie.

6. Important observation from the experiment

The rack.session cookie did not visibly change every time login/logout/browser restart was tested.

Therefore, do not use this rule:

"A session cookie must always change after every login or logout."

That is not a reliable general rule.

The correct QA approach is to inspect:

cookie attributes;

the Set-Cookie response;

the Cookie request header;

the actual server/application behavior.

The existence of a cookie in Application does not by itself prove that the server currently considers the session authenticated.

7. Cookie vs localStorage vs sessionStorage

Storage

Automatically sent with HTTP requests

JavaScript access

Typical persistence

Cookie

Yes, when applicable

Yes, unless HttpOnly

Depends on cookie lifetime

localStorage

No

Yes

Usually persists after browser restart

sessionStorage

No

Yes

Usually tied to the page/tab session

8. What I learned from the practical exercise

I connected the browser UI with the actual HTTP authentication flow:

Login
  ↓
authenticate request
  ↓
Response Headers
  ↓
Set-Cookie: rack.session=...
  ↓
Browser stores the cookie
  ↓
Application → Cookies
  ↓
rack.session

The key idea is that cookies are not just something visible in the browser. They participate directly in HTTP communication between browser and server.

9. QA checklist for cookies

When investigating cookies in DevTools, check:

[ ] Name
[ ] Value (do not publish real secrets)
[ ] Domain
[ ] Path
[ ] Expires / Max-Age
[ ] HttpOnly
[ ] Secure
[ ] SameSite
[ ] Set-Cookie in the response
[ ] Cookie in the request
[ ] Behaviour before login
[ ] Behaviour after login
[ ] Behaviour after logout

10. Security note

Do not publish real values of:

session cookies;

authentication tokens;

passwords;

API keys;

authorization headers;

other secrets.

For GitHub documentation, replace them with placeholders such as:

rack.session=<REDACTED>

11. Next topic

Cookies / Storage → HTTP

Next file:

http.md

The HTTP block should cover requests/responses, methods, headers, body, parameters, status codes, and how cookies fit into HTTP.