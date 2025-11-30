# SQL Injection Allowing for Login Bypass

## Disclaimer
All SQL injection examples, exploitation steps, and vulnerability demonstrations contained in this repository were 
performed exclusively in controlled, simulated lab environments created for educational and cybersecurity training purposes.

No testing was performed on live systems, third-party applications, or any environment without explicit authorization.
This content is intended solely to demonstrate secure coding practices, common vulnerabilities, and ethical penetration testing methodology.

### 1 - Summary
A SQL Injection vulnerability flaw in the login form allows attackers to bypass authentication easily. This flaw arose due to unsanatized user input being directly concatenated into the SQL query. Thus, forcing the query to grant access without the proper credentials.

## 2 - Environment
- Port Swigger SQLi Lab 'https://0a98001e031a862e82fa3da600da00a4.web-security-academy.net/'

## 3 - Preconditions
- There are no requred precondition other than the ability to access the login page.

## 4 - Reproduction
#### 1. Open 'https://0a98001e031a862e82fa3da600da00a4.web-security-academy.net/'
   
#### 2. Find the login page and begin testing

Simply fill in the login form and catch the HTTP request via any web proxy.

Notice within the footer of the POST request... we can directly see the login request as 'userneame=test&password=testtest'. 

I will forward this request to have a baseline response. From here we can edit the POST request and notice errors in handling and potential vulnerabilities.

What happens when we alter the Request?
First I will change the request slightly and see how the website reacts. I will simply put in ' directly after the username and compare the requests. The login POST request now reads 'userneame=test'&password=testtest'

After fowarded the edited request, we get an **Internal Sever Error**. Simply from adding a single quote to our username? This is a strong indecator of a SQLi vulnerability. The ' that we had entered is altering the strings within the query.

**Turning the original query from** - SELECT * FROM users WHERE username='test' AND password='testtest';
**Into this** - SELECT * FROM users WHERE username='test'' AND password='testtest';

Essentially turning the middle portion of the query into a string with incorrect synax. This is why after we forwarded our POST request the database threw an error. The user input request is being directly inserted into the backed SQL query, this improper input handling will be easy to exploit.

#### 3. Exploit the Vulnerability

Now that we have confirmed that the user input is reaching the SQL query directly, we can simply add out own query top the middle of the login statement and comment out anything else we dont want to execute.

Lets revisit the inital POST request so we can walkthrough the exploit. Once the HTTP request is captured we see - 
**'userneame=test&password=testtest'**

Here I will add a simple boolean test after the user name parameter of the POST request. Such as - 
**' OR 1=1'** since 1 will always equal 1 the query will return to true.

In addition to this I will comment out the remainder of the query to skip any additional authentication steps. Simply with -
**--** at the end my new boolean. This will ensure we are not requried to input the correct password.

Note * while -- will work in this instance, you would need to use diffrent inline comment syntax based on the specific SQL used by the database.

Finally we get out new post request that reads - 
**'userneame=test'+OR+1=1--&password=testtest'**

Simply combining our new boolean test with the inline comment and submit the request...

#### 4. Impact

Once submitted we are granted access through the login page with administative permissions. We are now able to change the email on the newly accessed admin account and do as we please. We can - 
- Access sensitive data
- Modify/delete data
- Bypass/disable additional security features
- Extract database contents

Note * that this injection will simply give you access to the first user account stored in the table. In this case it just so happened to be the administrator. Which was a nice easy way to also gain vertical privilage escilation with no additional effort.

#### 5. Root Cause

This vulnerability exists due to the applications use of unsanitized, user-supplied input directly inside of a dynamic SQL query. The login form uses simple string concatenation that can be manipulated easily. 

  SELECT * FROM users 
  WHERE username='test' 
    AND password='testtest';
    
The input is never validated or patameterized. We can input anything we want and the database will execute it. 

#### 8. Quick Fix 

Instead of embedding user input into the SQL string, developers must bind paramenters safely.

SELECT * FROM users 
WHERE username = ? AND password = ?;

This new structure will prevent user input from directly modifying the struture of the query, eliminating SQLi.

Note * that this is the simple fix to the problem, there are many more steps that should be taken such as- 
- Validating and sanatizing user input.
- Removing detailed SQL based error messages.
- Enforcing least-privilage database access.
- Implement proper authentication logic.
  
#### 9. Ethical Diclaimer

**All activity performed in a controlled, authorized enviorment. Do not use these techniques on systems you do not own or have explicit permission to test.**
