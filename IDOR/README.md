# IDOR (Insecure Direct Object Reference) — TryHackMe

**Room:** IDOR  
**Platform:** TryHackMe  
**Objective:** Learn what IDOR vulnerabilities are, how to discover them (including encoded IDs and unpredictable IDs), and how to exploit a lab scenario to retrieve a flag.

---

## 1. What is an IDOR?
**IDOR** stands for **Insecure Direct Object Reference**. It is a type of access control vulnerability that occurs when an application:

- Uses user-supplied input (for example a user ID or file name) to fetch objects (files, records, documents) from the server, **and**
- Trusts that input too much (no server-side validation to confirm the requester is authorized for that object).

If the server does not verify that the requested object belongs to the authenticated user, an attacker can manipulate the parameter to access other users’ data.

---

## 2. Simple example (query string IDOR)
Imagine you sign up and view your profile at:http://online-service.thm/profile?user_id=1305
If changing `user_id=1305` to `user_id=1000` lets you view another user’s profile, that is an IDOR. The application should check on the server side whether the logged-in user actually owns `user_id=1000` before returning that data.


📷 Screenshot:  
`![IDOR - account request](./screenshots/idor-account-request.png)`

---

## 3. Finding IDORs in Encoded IDs
Developers often **encode** IDs when passing them between pages (via query strings, POST fields, or cookies). A common encoding is **Base64**.

- Example: an encoded token might decode to `{"id":123,"role":"user"}`.
- Use a decoder (e.g., https://www.base64decode.org/) to reveal the payload, edit the `id`, re-encode with https://www.base64encode.org/, and resubmit the request to test for unauthorized access.


---

## 4. Finding IDORs in Unpredictable IDs
If IDs are not clearly visible or encoded, try this approach:

- Create **two accounts** (Account A and Account B).  
- Note their user IDs (from the API or page source).  
- While logged in as Account A, request resources using Account B’s ID.  
- If Account A can view Account B’s resources, you’ve got an IDOR.

This “swap test” is a reliable way to detect IDORs when direct observation fails.

---

## 5. Where IDORs are located
IDORs are not always obvious in the browser address bar — they can hide in:

- **AJAX requests** (XHR/fetch calls visible in the Network tab)  
- **JavaScript files** (endpoints referenced in scripts)  
- **Hidden parameters** added during development (parameter mining)  

Example: `/user/details` may use `user_id` as an optional parameter; if the endpoint accepts `user_id` and returns data based on it without verifying ownership, it’s vulnerable:
