# Unauthorized User Deletion via Broken Access Control

## Disclaimer
All examples, exploitation steps, and vulnerability demonstrations contained in this repository were 
performed exclusively in controlled, simulated lab environments created for educational and cybersecurity training purposes.

No testing was performed on live systems, third-party applications, or any environment without explicit authorization.
This content is intended solely to demonstrate secure coding practices, common vulnerabilities, and ethical penetration testing methodology.

### 1 - Summary
A simple yet criticle broken access control within a admin panel allows for verticle privelege escalation and unathorized user account deletion.

## 2 - Environment
- Port Swigger SQLi Lab 'https://0a77005804b23b728438a4fa00f100cc.web-security-academy.net/'

## 3 - Preconditions
- There are no requred precondition.

## 4 - Reproduction
#### - Open 'https://0a77005804b23b728438a4fa00f100cc.web-security-academy.net/'
   
#### - Find the robots.txt file
This is always a great starting point when searching for broken access controls. Many times the robots.txt file will give you alot of subdoamins that could be vulnerable.

In this case, after we have open the robots.txt file, we can see that there is a diallowed subdomain of /administrator-panel. Let see if we have access. Now I submit the edits URL of 
'https://0a77005804b23b728438a4fa00f100cc.web-security-academy.net/administrator-panel'. 

And I got in! I now have full access to the administrator panel.


## 5. Impact

Vertical privilage escilation to an admin page is a critical vulnerability. We can do a lot of damage in a short amount of time. 

The admin page that I was able to access shows all users that have ever used the website, and give me the functionality to delete them...

I was able to submit a new user and confirm that I could infact delete that user without being logged in throught the admin panel. A simple script could now delete the entire userbase.

## 6. Root Cause

This vulnerability inital arose do to the idea that robots.txt could be used as a security control. A robot.txt file is simply a voluntary guideline, there is nothing that stops someone from actually going to the disallowed URLS.

After this the application failed to verify the users roles & permissions. First when visiting the /admin-panel and second when submitted elete request from that panel.

The application is missing critical server side authorization enforment for admin endpoints.

## 7. Quick Fix 
Add proper server-side authorization checks to all admin endpoints and remove sensitive paths from robots.txt, ensuring the admin panel is only accessible to authenticated and authorized admin users.

(idAdmin == TRUE)

## 8. Ethical Diclaimer

**All activity performed in a controlled, authorized enviorment. Do not use these techniques on systems you do not own or have explicit permission to test.**
