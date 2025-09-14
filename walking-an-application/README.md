# TryHackMe – Walking an Application

In this room, I learned how to manually review a web application for security issues using **only in-built browser tools**. Automated scanners miss a lot, so this room helped me practice *manual testing techniques*.

---

## 1. Introduction
Tools I practiced with:
- **View Source** – human-readable code of a webpage
- **Inspector** – inspect and edit page elements
- **Debugger** – analyze and control JavaScript
- **Network** – monitor network requests

(No screenshot for this step)

---

## 2. Exploring the Website
I explored the Acme IT Support site and noted down important features:

| Feature           | URL                 | Summary |
|-------------------|---------------------|---------|
| Home Page         | `/`                 | Overview of Acme IT Support |
| Latest News       | `/news`             | Shows recent articles |
| News Article      | `/news/article?id=1`| Individual articles (some restricted) |
| Contact Page      | `/contact`          | Contact form with name, email, message |
| Customers         | `/customers`        | Redirects to login page |
| Customer Login    | `/customers/login`  | Login form |
| Customer Signup   | `/customers/signup` | Create new user |
| Reset Password    | `/customers/reset`  | Password reset form |
| Dashboard         | `/customers`        | User’s submitted tickets |
| Create Ticket     | `/customers/ticket/new` | Ticket form with file upload |
| Account           | `/customers/account` | Edit profile |
| Logout            | `/customers/logout` | Logs out the user |

📸 Screenshot:  
![Acme Homepage](Screenshot%202025-09-05%20065909_1.jpg)

---

## 3. Viewing Page Source
I examined the HTML, CSS, and JavaScript. Key findings:
- Found **developer comments** in the source code.
- Discovered a **hidden link starting with "secr"**.
- Found a **directory listing misconfiguration** (revealed files).
- Identified the **framework in use** and version info (outdated).

📸 Screenshot:  
![Page Source](Screenshot%202025-09-05%20070441_1.jpg)

---

## 4. Using Developer Tools – Inspector
- Opened the **Inspector** to analyze live HTML/CSS.  
- Removed the **premium content paywall** by changing `display: block` → `display: none`.  
- Revealed hidden content and captured a flag.

📸 Screenshot:  
![Inspector](Screenshot%202025-09-06%20045730_1.jpg)

---

## 5. Using Developer Tools – Debugger
- Investigated the **flash.min.js** script.  
- Used **Pretty Print** to format the obfuscated JavaScript.  
- Set a **breakpoint** to stop execution.  
- This prevented the red popup from disappearing, revealing a flag.

📸 Screenshot:  
![Debugger](Screenshot%202025-09-06%20050520_1.jpg)

---

## 6. Using Developer Tools – Network
- Monitored **network requests** on the Contact Page.  
- Observed the form submission via **AJAX**.  
- Tracked the request to its destination and discovered another flag.

📸 Screenshot:  
![Network](Screenshot%202025-09-06%20052716_1.jpg)

---

## Final Thoughts
This room helped me understand how powerful **manual browser-based testing** can be.  
I practiced:
- Reading **page source** for leaks
- Using **Inspector** to bypass paywalls
- Debugging JavaScript with **breakpoints**
- Tracking background requests via **Network tab**

---


