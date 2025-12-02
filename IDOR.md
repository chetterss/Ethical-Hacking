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
- Note that this chat room has the option for us to view/download the transcript for the conversation. Lets download a transciprt and review the HTTP history.
- We find a get request that is used to download the transcript. Within the header we easily see *'GET /download-transcript/6.txt'*. What if we change the 6 to a diffrent number?
- We increment the get request all the way down trying 5.txt, 4.txt ... and finally we attemp 1.txt.
- We are now viewing a chat transcipt that is not ours. We are even seeing sensitive user data being discussed in the chat.

#### 5 - Impact
Now that we have gained acces to a users password and username from thier chat transcripts we can now login to their account and do as we please. As this site is a online retailer was can order items to our own address with their credit card info, update the email on their account, or in the worse case we may even be able to exfoltrate their entirety of their credit card information. While this kind of horizontal privilage escilation my not directly hurt the company hosting the website. It can be very detremental for the user and even lead to a lose of reputation and trust from the public.
