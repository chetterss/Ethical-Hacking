## Insecure Direct Object Reference for Horizontal Privilage escilation

### Disclaimer
All IDOR examples, exploitation steps, and vulnerability demonstrations contained in this repository were performed exclusively in controlled, simulated lab environments created for educational and cybersecurity training purposes.

No testing was performed on live systems, third-party applications, or any environment without explicit authorization. This content is intended solely to demonstrate secure coding practices, common vulnerabilities, and ethical penetration testing methodology.

#### 1 - Summary
An IDOR vulnerability within a user chat form transcipt can be used to gain horizontal privilage escilation. This vulnerability exists do to the back end server treating the filename as a trusted input source. Even though it can be easily changed by an attacker.

#### 2 - Enviornment
- Port Swigger IDOR Lab 'https://0a8a005a040cf95c80df12a0002c0054.web-security-academy.net/'

#### 3 - Preconditions
- There are no preconditions required to expliot this vulnerability. You are not even required to login.

#### 4 - Reproduction
- Go to the lab website at 'https://0a8a005a040cf95c80df12a0002c0054.web-security-academy.net/'
- After poking around we notice a live chat portion of the website. Let start chatting and see if there is any exposed user details.
- Note that this chat room has the option for us to view/download the transcript from the conversation. Lets download a transciprt and review the HTTP history.
- We find a GET request that is used to download the transcript. Within the header I can see *'GET /download-transcript/6.txt'*. What if I change the 6 to a diffrent number?
- Next I increment the GET request all the way down trying 5.txt, 4.txt ... and finally we attemp 1.txt. And it works...
- We are now viewing a chat transcipt that is not ours. We are even seeing sensitive user data being discussed in the chat.

#### 5 - Impact
Now that we have gained acces to a users password and username from their chat transcripts we can login to their account and do as we please. As this site is a online retailer was can order items to our own address with their credit card info, update the email on their account, or in the worse case we may even be able to exfoltrate the entirety of their credit card information. This kind of horizontal privilage escilation can be a major problem.

#### 6 - Root Cause
The application is relying on user supplied file names within GET request and is not verifying that the authenticated user is authorized to access the specific file. This allows attackers to modify the parameters of the request and access files that do not belong to them.

Simply put - the missing authorization logic on the file access endpoint is where this IDOR stems from.

#### 7 - Quick Fix
Developers must verify that the authenticated user should have acces to the file that they are requesting. They should have implemented a server-side authorization check that ensures the requested files belong to the logged-in user. All before even thinking about serving the request. Something like this - 

file = database.getFileByName(request.filename)

if file.owner_id != current_user.id:
    return 403 Forbidden

#### 8 - Ethical Disclaimer

**All activity performed in a controlled, authorized enviorment. Do not use these techniques on systems you do not own or have explicit permission to test.**
