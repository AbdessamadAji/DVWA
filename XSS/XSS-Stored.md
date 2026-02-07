# XSS (Cross-Site Scripting)

XSS is a web vulnerability where an attacker injects malicious JavaScript into a website, and that script runs in the victim's browser.

Basically: The site trusts user input → attacker puts JS in that input → browser executes it.

## Stored XSS

Same thing but payload is saved on server and affects everyone, not reflected or run only in victim's browser like DOM XSS.

## Objective

Redirect everyone to a web page of your choosing.

## LOW

The website has a comment feature, anyone can post anything:

<img width="759" height="337" alt="image" src="https://github.com/user-attachments/assets/51394302-08d0-4710-8365-8cb7e412919d" />

So let's test if that name or message parameters don't sanitize or filter inputs.

Injecting something like: `<script>alert(document.cookie)</script>` in the message (you can achieve same results on name input):

<img width="1301" height="305" alt="Screenshot from 2026-02-07 18-21-21" src="https://github.com/user-attachments/assets/4ac07068-f245-4468-8630-86c48b83456b" />


So everyone who visits this page, a window will pop up and show them their cookies.

But instead of this, I should redirect every visitor to: https://portswigger.net/web-security/cross-site-scripting/stored

So posting a comment with: `<script>window.location="https://portswigger.net/web-security/cross-site-scripting/stored"</script>` will do the work.

### But Why the Redirect Trick?

Here are some scenarios about why an attacker would chain a stored XSS with a redirection:

#### Session Hijacking via Redirect

In a Stored XSS scenario, the attacker injects a persistent JavaScript payload into the application (for example through comments or profile fields). When victims later load the infected page, the script executes automatically and redirects their browser to an attacker-controlled server. During this redirection, the victim's browser sends request headers and any accessible session cookies associated with the vulnerable application. The attacker captures these session tokens and replays them to impersonate the victim. This results in full account takeover, often bypassing MFA, without installing malware or requiring user interaction.

#### Using the Browser as an Authenticated Attack Proxy (Client-Side Request Forgery)

JavaScript cannot perform raw network scanning like Nmap. However, Stored XSS allows attackers to abuse browser trust. An employee's browser already has access to internal resources such as printers, routers, and intranet applications that are unreachable from the internet. The injected JavaScript uses the victim's browser as a proxy by sending HTTP requests to these internal systems using the victim's existing authentication context. This technique is known as Client-Side Request Forgery. Instead of port scanning, the attacker performs authenticated abuse by forcing the browser to submit forms, trigger actions, or interact with internal dashboards, effectively pivoting into the internal network.

## MEDIUM

It appears that the developer filtered the word `script` and fixed the issue for the comment field, but this can easily be bypassed using case variations, injecting `ScRiPt` instead of `script`.

So injecting: `<ScRiPt>alert(document.cookie)</ScRiPt>` will result in displaying every visitor's cookies:

<img width="1370" height="344" alt="Screenshot from 2026-02-07 18-54-15" src="https://github.com/user-attachments/assets/ed8f4542-3225-4ac5-b68d-9598084ad1b5" />

And to redirect them we can use:
```html
<sCrIpt>window.location="https://portswigger.net/web-security/cross-site-scripting/stored"</scRiPt>
```

(You can use Burp Suite or just increase the max length by inspecting the name field)

<img width="980" height="772" alt="Screenshot from 2026-02-07 18-57-49" src="https://github.com/user-attachments/assets/614a590d-ba70-4926-8e0d-f09821ff2ace" />

## HIGH

The developer believes they have disabled all script usage by removing the pattern `<s*c*r*i*p*t>`.

So we won't use script here, instead I will use: `</select><img src/onerror = alert(document.cookie)>` (since our input is inserted inside `<select>` so we should close it first).

<img width="1512" height="921" alt="Screenshot from 2026-02-07 19-06-22" src="https://github.com/user-attachments/assets/ad33d202-fa94-4d6c-81de-08c6f6fef368" />


Now let's redirect those poor visitors to read about stored XSS and why it's dangerous, by injecting in the name field:
```html
</select><img src=x onerror="location='https://portswigger.net/web-security/cross-site-scripting/stored'">
```
