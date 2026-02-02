# What is a CAPTCHA?

A CAPTCHA is a program that can determine whether its user is a human or a computer. You've probably seen them—colorful images with distorted text at the bottom of web registration forms. CAPTCHAs are used by many websites to prevent abuse by "bots," which are automated programs usually written to generate spam. No computer program can read distorted text as well as humans can, so bots cannot navigate sites protected by CAPTCHAs (although some CNN-based bots are now able to bypass them).

CAPTCHAs are often used to protect sensitive functionality from automated bots. Such functionality typically includes user registration, profile changes, password changes, and content posting. In this example, the CAPTCHA is guarding the password change functionality for the user account. This provides limited protection against CSRF attacks, as well as against automated bot guessing.

## Objective

Change the current user's password in an automated manner because of the poor CAPTCHA system.

## LOW

I filled in the password change form and solved the CAPTCHA, then captured the requests in Burp Suite.

<img width="1570" height="563" alt="Screenshot from 2026-02-02 17-43-07" src="https://github.com/user-attachments/assets/63acb9cc-e4de-4237-9714-776424e93cfd" />

I noticed a `step=1` variable and tried changing it to `step=2`.

<img width="1551" height="504" alt="Screenshot from 2026-02-02 17-43-27" src="https://github.com/user-attachments/assets/547c5766-472b-4caa-b141-9dc4d40a90a0" />

This appeared to bypass step 1, which is the CAPTCHA step, and step 2 is the password change step.

## MEDIUM

By intercepting the request in Burp Suite, I notice a parameter that's checking if the user has successfully: `passed_captcha=true`

So doing the same trick as before to skip step 1 and ensuring `passed_captcha` is set to `true` will bypass the CAPTCHA check.

<img width="1566" height="795" alt="Screenshot from 2026-02-02 18-05-14" src="https://github.com/user-attachments/assets/09f74b21-cca9-41f6-8ff5-2608583b653d" />

## HIGH

I think the previous bugs were fixed, so I got stuck here for a while, until I decided to check the source code:

<img width="1918" height="889" alt="image" src="https://github.com/user-attachments/assets/7bd8de98-5901-4034-924e-9ae86351c4d3" />

This if condition was screaming:
```php
if (
    $resp || 
    (
        $_POST[ 'g-recaptcha-response' ] == 'hidd3n_valu3'
        && $_SERVER[ 'HTTP_USER_AGENT' ] == 'reCAPTCHA'
    )
)
```

Basically, it's a logic flaw testing code that acts like a backdoor to skip the CAPTCHA verification every time, but they forgot to remove it in production.

So, setting the User-Agent to `reCAPTCHA` and `g-recaptcha-response` to `hidd3n_valu3` provided that backdoor and bypassed the CAPTCHA check.

<img width="1552" height="869" alt="image" src="https://github.com/user-attachments/assets/3306efa8-a51d-404e-a106-1476bd7025fa" />
