XSS (Cross-Site Scripting):
XSS is a web vulnerability where an attacker injects malicious JavaScript into a website, which then runs in another user’s browser, in order to steal cookies / sessions, impersonate the victim...

DOM-Based XSS
DOM XSS happens when JavaScript in the browser takes untrusted input and writes it into the page without sanitization, and it takes place on the client side, nothing goes to the server.

(said by DVWA creator):
DOM Based XSS is a special case of reflected XSS where the JavaScript is hidden in the URL and pulled out by JavaScript in the page while it is rendering rather than being embedded in the page when it is served. This can make it stealthier than other attacks, and WAFs or other protections which are reading the page body do not see any malicious content.

Objective
I should run my own JavaScript in another user's browser, use this to steal the cookie of a logged in user.

