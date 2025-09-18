Cross-Site Scripting (XSS) — TryHackMe

Room: Cross-Site Scripting (XSS)
Platform: TryHackMe
Objective: Learn the different types of XSS vulnerabilities, how to craft payloads, bypass filters, and exploit them in real-world scenarios.


---

1. Introduction 

XSS is based on JavaScript.

Cross-Site Scripting (XSS) is an injection attack where malicious JavaScript is injected into a web application with the goal of being executed by other users.

XSS vulnerabilities are extremely common and have been reported in major applications.

---

2. Payloads

In XSS, the payload is the JavaScript code executed on the victim’s machine.

Types of intentions:

Proof of Concept (PoC): <script>alert('XSS');</script>

Session Stealing: Capture cookies with document.cookie.

Keylogger: Capture keystrokes from the victim.

Business Logic Abuse: Manipulate app logic (e.g., change email).


Payloads can be modified depending on the injection point and filters present.

---
3. Reflected XSS

Occurs when user input is immediately reflected in a web page without validation.

Example: An error message pulled directly from a query string.

Attacker injects JavaScript via URL parameter.

Victims execute the malicious script when visiting the crafted link.


Impact: Theft of session info, redirection to malicious sites.


---

4. Stored XSS

Happens when the malicious payload is stored in the application (e.g., comments, profile info) and executed every time the page is loaded by other users.

Impact:

Stealing session cookies.

Redirecting users.

Automating malicious actions.


Testing method: Inject payloads in stored fields (comments, profiles, listings).


---

5. DOM-Based XSS

Execution happens entirely in the browser when JavaScript code on the page dynamically handles malicious input.

Example: Input from window.location.hash written directly to the DOM.

Impact: Craft links that steal data or cookies without backend interaction.

Testing method: Inspect client-side JavaScript for unsafe handling of inputs.


---

6. Blind XSS

Similar to Stored XSS, but the attacker cannot see the payload execution. Instead, it runs in an environment only accessible by admins or staff.

Example: Injecting a payload in a support ticket form. When staff view it in their portal, the payload executes.

Impact:

Exfiltrate cookies.

Steal internal portal data.

Hijack staff sessions.


Testing method: Use payloads that callback to an attacker-controlled server. Tools like XSS Hunter Express can be useful.
