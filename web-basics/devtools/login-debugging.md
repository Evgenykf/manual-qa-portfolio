Web — DevTools: Login Debugging

Practice exercise in using Chrome DevTools to understand what happens when the Login form is submitted. I used The Internet and followed the action from the page to the request sent by the browser.

Requirements

Inspect the Username field and Login button in Elements.

Use Console to inspect the page with simple JavaScript selectors.

Find the real login request in Network.

Check the method, status, form data and response headers.

Explain the flow from clicking Login to the error shown on the page.

Elements

Username

<input type="text" name="username" id="username">

input, type="text", name="username", id="username".

Login button

<button class="radius" type="submit">
    <i class="fa fa-2x fa-sign-in"> Login</i>
</button>

button, class="radius", type="submit". The important part here is type="submit" because this button submits the form.

Console

I tried a few simple commands to get used to working with the page through JavaScript. The results were:

document.title                              // "The Internet"
document.querySelector('input')             // <input type="text" name="username" id="username">
document.querySelector('button')            // <button class="radius" type="submit">...</button>
document.querySelector('button').textContent // " Login"

querySelector() returns the first element that matches the selector, so it is useful for quickly checking elements from the Console.

Network

At first I saw GET /login with 200 OK, but that request was only loading the page. After entering the test credentials and clicking Login, the request I needed was:

POST https://the-internet.herokuapp.com/authenticate
Status: 303 See Other
username = test@gmail.com
password = [test credential]
Location: https://the-internet.herokuapp.com/login

The Response tab was empty. After the redirect, the page showed:

Your username is invalid!

What I found

The Login action sends a POST request to /authenticate. The server responds with 303 See Other and redirects the browser back to /login. So the flow is:

Click Login → POST /authenticate → 303 → /login → login error

The useful part of the exercise was learning to follow the problem through Network instead of stopping at the message on the page. Now I can check what the browser actually sent and how the server responded.

DevTools used

Elements

Console

Network

Next: Application → Cookies / Local Storage / Session Storage