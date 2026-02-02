# File Upload Vulnerability

A file upload vulnerability occurs when a web application allows users to upload files without properly validating the file's type, content, size, or name.

Attackers can exploit this to upload malicious files (web shells, malwares, etc.) that the server later executes or processes, leading to serious security issues.

## LOW

For testing I created a simple PHP file that executes operating system commands on the server.

<img width="601" height="74" alt="Screenshot from 2026-02-01 20-58-10" src="https://github.com/user-attachments/assets/fba73876-850e-4230-bc16-4963426b7fc8" />

I just uploaded it.

<img width="759" height="222" alt="Screenshot from 2026-02-01 20-59-51" src="https://github.com/user-attachments/assets/5b693ae6-2528-4579-8625-c0be57d6bb2f" />


And accessed it, no security measures to check and sanitize what user uploads.

<img width="631" height="133" alt="Screenshot from 2026-02-01 21-00-58" src="https://github.com/user-attachments/assets/95761282-4604-4ae0-84ef-63e8060c4de9" />


## MEDIUM

I tried to upload my `justtest.php` file, but I think the backend checks the extension of the uploaded file.

<img width="769" height="227" alt="Screenshot from 2026-02-01 21-04-30" src="https://github.com/user-attachments/assets/0907168c-0c64-4dff-83a3-648e099b9b3e" />


Analyzing the HTTP response, the Content-Type caught my eyes


<img width="1567" height="862" alt="Screenshot from 2026-02-01 21-06-14" src="https://github.com/user-attachments/assets/b8796765-ca02-4dc4-9111-38b768375263" />


so I tried changing it from `application/x-php` to `image/jpeg`.

<img width="1567" height="862" alt="Screenshot from 2026-02-01 21-06-42" src="https://github.com/user-attachments/assets/8342e27f-7942-474c-be13-6e1ed41ca4b1" />


And it worked, because the backend checks that Content-Type, it should match `image/jpeg`.

<img width="582" height="121" alt="Screenshot from 2026-02-01 21-08-13" src="https://github.com/user-attachments/assets/1f6b9264-3c96-4677-be71-8bc66b599a68" />


## HIGH

I tried to do the same previous technique I used in medium level, but didn't work, so they're using other ways to ensure the uploaded file is an image.

By doing some researches, I found that one way is to check the file signature, like first 4 or 8 bytes if they match the wanted type.

<img width="1631" height="935" alt="Screenshot from 2026-02-02 16-34-36" src="https://github.com/user-attachments/assets/c9043baf-fad4-4b99-a279-83562c05bb50" />

Using `hexedit`, I changed first 8 bytes of my `justtest.php` file to match the PNG signature.

<img width="1833" height="431" alt="Screenshot from 2026-02-02 16-24-43" src="https://github.com/user-attachments/assets/eec8b225-279e-48f1-8131-b3883fe40dc7" />

I suspected that they checked the extension too, so I changed my file's extension to match an image.

<img width="804" height="164" alt="Screenshot from 2026-02-02 16-26-34" src="https://github.com/user-attachments/assets/8a8b4cc7-8831-4e8f-a3f7-44d3e0bbeba6" />

Then I uploaded it and intercepted the request with Burp Suite, to add null bytes to the extension so it will ignore the added `.jpg`, it's added just to match the backend's rules.

<img width="1557" height="862" alt="Screenshot from 2026-02-02 16-29-24" src="https://github.com/user-attachments/assets/0df44d99-50c1-4820-8bb3-99147fecd8f5" />

Then I sent the request and accessed it, and it's working.

<img width="1557" height="862" alt="Screenshot from 2026-02-02 16-30-32" src="https://github.com/user-attachments/assets/e3fc6cb0-8275-4eb7-9fcf-9a7b47c846a5" />
