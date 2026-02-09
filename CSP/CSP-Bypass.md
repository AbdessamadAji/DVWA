Content Security Policy (CSP) Vulnerability
CSP is a browser security feature that tells the browser which sources are allowed to load content (JS, CSS, images, etc.).
It’s mainly used to reduce XSS and data injection attacks.
A CSP vulnerability happens when the policy is misconfigured or too permissive, allowing attackers to bypass CSP and execute malicious code.

Objective
Bypass Content Security Policy (CSP) and execute JavaScript in the page.

LOW:
First things first, this lab provides a feature to execute external JS scripts.
pic here

But we should check the CSP policy, so by clicking Ctrl + Shift + I, then going to the Network tab and checking csp/, we can see pastebin, example.com, and code.jquery.com…
pic here

So I tried to create a paste on Pastebin containing a very simple JS:
alert("CSP BYPASS WORKED");

pic here

And I used the raw URL as an input, but nothing happened.

I checked the Network tab to see what was happening, and I found that the script was loaded, but it was not executed.
And this is because Pastebin serves files as text, not as JS:
Content-Type: text/plain

pic here

We can also see:
X-Content-Type-Options: nosniff

which means the browser is FORBIDDEN from guessing the type.
This is called MIME Type Blocking (modern browsers do it by default).

Basically, the CSP bypass is working, because the JS file was loaded, but due to this MIME type blocking, it can’t be executed.

I tried GitHub Gist, but since it’s not CSP whitelisted, it didn’t work (it was obvious it wouldn’t, but GitHub Gist serves files with their real extensions, not as text, so it was worth the try):
pic here

Then I tried loading a code.jquery.com script like:
https://code.jquery.com/jquery-3.6.0.min.js

And by checking the Network tab and then the Response, I could see the JS code there.
pic here

So I think there is an issue with the lab — maybe it’s outdated, because we can’t use Pastebin anymore for this purpose.

MEDIUM:
after examining the csp/ , and there is a nonce , A nonce (number used once) is a random value generated per page load.
basically Only scripts that contain this exact nonce are allowed to run.
so our script should contain that nonce , something like:
<script nonce="TmV2ZXIgZ29pbmcgdG8gZ2l2ZSB5b3UgdXA="> alert(1); </script>

but since pastebin isn't working as expected , there is nothing much I can do here

HIGH:

