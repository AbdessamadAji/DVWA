# Authorization Bypass Vulnerability

Authorization Bypass happens when a user can access data or actions they should NOT be allowed to, because access controls are missing, weak, or only enforced on the frontend.

## Objective

Your goal is to test this user management system at all four security levels to identify any areas where authorization checks have been missed.

The system is only designed to be accessed by the admin user, so have a look at all the calls made while logged in as the admin, and then try to reproduce them while logged in as different user.

If you need a second user, you can use `gordonb / abc123`.

## LOW
we should access this (display it or modify it) using a non admin user 

<img width="695" height="349" alt="Screenshot from 2026-02-10 19-07-20" src="https://github.com/user-attachments/assets/d1c6482c-348c-4399-9a75-5a7f9004bf29" />


First let's login as gordonb and see what we can do.

And let's visit `authbypass/`:

<img width="1034" height="820" alt="image" src="https://github.com/user-attachments/assets/2bf21217-69bb-40dc-85c3-b68af8e0ea9e" />


Basically, it should be a hidden page, like if we keep it hidden, then it's secured, but there are a lot of ways to find hidden pages, panels, dashboards, etc.

## MEDIUM

When I tried to access http://127.0.0.1/dvwa/vulnerabilities/authbypass/, it displayed:

<img width="481" height="83" alt="Screenshot from 2026-02-10 19-23-59" src="https://github.com/user-attachments/assets/5a8948d8-8871-4dd3-99dc-34926a13d92d" />

So let's see what's happening, and let's login as admin, and access authbypass:

<img width="1545" height="446" alt="Screenshot from 2026-02-10 19-26-16" src="https://github.com/user-attachments/assets/53a932e2-5e8c-4c72-9881-e6b1e61830b3" />

We can see `get_user_data.php`, so let's reproduce this using gordonb:

<img width="1911" height="89" alt="Screenshot from 2026-02-10 19-28-30" src="https://github.com/user-attachments/assets/e62e61dc-bb28-44f5-a005-2f49d28fba93" />


## HIGH

Let's access as admin, and see what's happening in the backstage.

Here is the request for `get_user_data.php`:

<img width="1557" height="436" alt="Screenshot from 2026-02-10 19-33-24" src="https://github.com/user-attachments/assets/dfdb689c-0a45-44ad-afc9-e9f33c897b00" />


And this is the request for `change_user_details.php`:

<img width="1557" height="493" alt="Screenshot from 2026-02-10 19-33-56" src="https://github.com/user-attachments/assets/6983a69e-38b3-44ec-85a7-8d478b97d258" />


Let's try to reproduce this as gordonb.

For `get_user_data.php`:

<img width="582" height="87" alt="Screenshot from 2026-02-10 19-34-47" src="https://github.com/user-attachments/assets/6ed7efca-5244-41b1-aed3-9044c51941ee" />


And for `change_user_details.php`:

<img width="639" height="123" alt="Screenshot from 2026-02-10 19-35-19" src="https://github.com/user-attachments/assets/ba4b89be-c8e0-4ed2-bc62-c5f67da5772a" />


So let's change that request to POST and try to change something like Bob's surname:

<img width="1534" height="584" alt="Screenshot from 2026-02-10 19-36-43" src="https://github.com/user-attachments/assets/976fb23e-781a-427f-b825-04e43dcdaed1" />


And this is the result, after seeing the dashboard as admin:

<img width="1034" height="820" alt="Screenshot from 2026-02-10 19-37-34" src="https://github.com/user-attachments/assets/c642d526-52c5-4abc-96d9-9d9abe58ff0f" />
