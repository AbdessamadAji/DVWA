# Weak Session IDs (Session Management Vulnerability)

Weak Session IDs occur when a web application generates session identifiers that are predictable, guessable, or not random enough, allowing attackers to hijack user sessions.

## (Said by DVWA Creators)

Knowledge of a session ID is often the only thing required to access a site as a specific user after they have logged in. If that session ID is able to be calculated or easily guessed, then an attacker will have an easy way to gain access to user accounts without having to brute force passwords or find other vulnerabilities such as Cross-Site Scripting.

## Objective

This module uses four different ways to set the dvwaSession cookie value. The objective of each level is to work out how the ID is generated and then infer the IDs of other system users.

## LOW

The ID is literally written as a digit in the cookie, and keeps incrementing each time. The ID is incremented by 1 every time we generate a new one, starting with 1.

<img width="1611" height="117" alt="Screenshot from 2026-02-03 19-02-51" src="https://github.com/user-attachments/assets/6f66f31e-1ef1-47f5-9aae-cca417071bbf" />


## MEDIUM

After observing a few IDs (1770142601, 1770142610, 1770142611), it appears that it is: Unix epoch time.

That basically allows to guess other user IDs, so attackers can keep guessing the ID and accessing other users' data.

<img width="1615" height="124" alt="Screenshot from 2026-02-03 19-21-16" src="https://github.com/user-attachments/assets/a0b1d0ad-bafc-44f5-89f0-74d2b73c8e91" />


## HIGH

First thing we always do is observing.

<img width="1648" height="155" alt="Screenshot from 2026-02-03 19-32-42" src="https://github.com/user-attachments/assets/efc4f4b8-fd00-4c47-8346-83bdcef2c8b4" />


So I listed some sessions and analyzed them:
```
c4ca4238a0b923820dcc509a6f75849b
c81e728d9d4c2f636f067f89cc14862c
eccbc87e4b5ce2fe28308fd9f2a7baf3
a87ff679a2f3e71d9181a67b7542122c
e4da3b7fbbce2345d7772b0674a318d5
1679091c5a880faf6fb5e6087eb1b2dc
```

And that seems like an MD5, so let's check, as an example: `8f14e45fceea167a5a36dedd4bea2543`

<img width="1052" height="324" alt="Screenshot from 2026-02-03 19-33-49" src="https://github.com/user-attachments/assets/591ea0c3-f116-4fb8-800d-637f100b254f" />
