DevTools — Login Debugging

Practice in using Chrome DevTools to find out what happens when a login form is submitted. I used the Login page on The Internet and followed the action from the page itself to the HTTP request.

Page

Login form contains:

<input type="text" name="username" id="username">

and:

<button class="radius" type="submit">
    <i class="fa fa-2x fa-sign-in"> Login</i>
</button>

The important part here is not just finding the elements, but being able to connect them with what happens after the click.

Console

Checking the page title

document.title

Result:

The Internet

Finding the username field

document.querySelector('input')

Result:

<input type="text" name="username" id="username">

Finding the Login button

document.querySelector('button')

Result:

<button class="radius" type="submit">...</button>

Reading the button text

document.querySelector('button').textContent

Result:

 Login

Notes

querySelector() returns the first element matching the selector.

This was useful for checking how the element is represented in the DOM instead of looking only at what is visible on the page.

Network

After entering test credentials and clicking Login, I looked for the request created by the action.

Request

Request URL:
https://the-internet.herokuapp.com/authenticate

Request Method:
POST

Status Code:
303 See Other

Form data

username = test@gmail.com
password = [test credential]

Response headers

Location:
https://the-internet.herokuapp.com/login

The Response tab did not contain a response body.

What happens after clicking Login?

The flow looks like this:

Click Login
    ↓
POST /authenticate
    ↓
credentials are sent
    ↓
303 See Other
    ↓
Location: /login
    ↓
browser returns to Login page

The page then shows:

Your username is invalid!

Notes

The interesting part here was not the 200 OK responses for page assets. The useful request was POST /authenticate.

303 See Other means the browser is told to follow another URL. In this case the Location header points back to /login, which matches the visible login error.

This is a good example of why the Network tab is useful for QA: instead of stopping at "the button does not work", I can see which request was sent, what the server returned, and where the browser was redirected.

What I checked in DevTools

Elements — found the username field and Login button

Console — inspected the page and DOM elements

Network — found the login request

Request method — POST

Status code — 303

Form data — username and password were sent

Response headers — redirect goes back to /login

What I learned

The main thing I wanted to understand was the path from a UI action to a server response:

UI → request → server response → browser → UI

That makes it much easier to investigate login problems and similar web issues instead of checking only