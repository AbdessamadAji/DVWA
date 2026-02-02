# SQL Injection

SQL Injection is a vulnerability where an attacker "injects" malicious SQL code into an input field, which is then executed by the backend database. This happens when user input is concatenated directly into a database query instead of being properly sanitized.

## Main Types of SQLi

- **In-Band (Classic)**: The attacker uses the same communication channel to launch the attack and gather results (e.g., seeing data directly on the page), that's what we're facing here.
- **Inferential (Blind)**: The attacker observes the server's response (True/False or Time Delays) to reconstruct the database structure.
- **Out-of-Band**: The attacker makes the database send data to an external server they control (rare).

## Objective

There are 5 users in the database, with IDs from 1 to 5. My mission is to steal their passwords via SQLi.

## LOW

The functionality is that by entering a user ID, it shows their ID, first name, and surname.

<img width="743" height="162" alt="Screenshot from 2026-02-02 18-32-51" src="https://github.com/user-attachments/assets/fd94709d-9d02-4825-9349-f21a0818ff00" />

I tried entering `1'`, which triggered an SQL syntax error and indicated that there is an SQL injection vulnerability. Now I need to customize my syntax.

<img width="1341" height="103" alt="Screenshot from 2026-02-02 18-34-00" src="https://github.com/user-attachments/assets/acb595fc-84cd-4b80-a4c5-4179f748b24b" />

The next step is identifying how many columns are returned from the database. We can use `ORDER BY n--` and keep increasing `n` (where `n` is an integer) until we get an error. The number before the error is the number of columns.

<img width="1340" height="106" alt="image" src="https://github.com/user-attachments/assets/e8c55c74-91eb-46a4-a5da-3ea90fa36b1c" />

So, we can use a UNION to get usernames and passwords:
```sql
1' UNION SELECT user, password FROM users#
```

<img width="1030" height="808" alt="Screenshot from 2026-02-02 18-49-12" src="https://github.com/user-attachments/assets/30c003ef-1dee-45ac-93ad-7ff6b975f5ab" />

## MEDIUM

<img width="774" height="157" alt="Screenshot from 2026-02-02 18-51-02" src="https://github.com/user-attachments/assets/ebb24405-ccee-415b-a0b7-21799bf8b51a" />

To prevent and fix the previous issue, developers decided to force user to check from a list instead of entering values, which caused SQL injection, seems good, but not enough.

So let's intercept that request with Burp Suite, and send it to repeater. and let's do the same trick to see if a SQLi is granted, by injecting `1'`:

<img width="1548" height="574" alt="image" src="https://github.com/user-attachments/assets/bdb1af05-1d75-40c2-86f0-4bd9952a2a2b" />

Now all we need is to craft a clean and syntactically correct injection.

Something like:
```
1+UNION+SELECT+user,password+FROM+users
```

<img width="1559" height="785" alt="image" src="https://github.com/user-attachments/assets/4a47ee41-547a-41ba-95d8-3c891d0f7ae7" />

## HIGH

Now they're trying to fix the previous issues by redirecting the request to a new page, maybe to prevent automation tools like sqlmap.

This is called **Session-Based Injection**:

- **Input Page**: You enter your payload (e.g., `1' OR '1'='1'`). The server saves this string into your `$_SESSION` variable.
- **Display Page**: When you open the second window, the server pulls the payload out of the Session and drops it into the SQL query.

So let's put our hands in. I intercepted the request via Burp Suite and injected our SQL payload into the new pop-up window:


<img width="1539" height="827" alt="Screenshot from 2026-02-02 19-08-34" src="https://github.com/user-attachments/assets/0278672a-ff9e-4b7f-abc5-157a04c69ffc" />

And the passwords are appearing.

<img width="1175" height="807" alt="Screenshot from 2026-02-02 19-08-49" src="https://github.com/user-attachments/assets/a53124d1-367b-4f9c-a782-69c51843590d" />

# Cracking the Passwords

By just seeing it looks like MD5, taking the example of: `098f6bcd4621d373cade4e832627b4f6`

I'm lazy now so let's try something like CrackStation.

<img width="1202" height="381" alt="image" src="https://github.com/user-attachments/assets/d25392f9-d619-4e73-ba8a-7c98935a02d2" />

And doing exactly the same thing for all the passwords, we got:
```
admin:test
gordonb:abc123
1337:charley
pablo:letmein
smithy:password
```


