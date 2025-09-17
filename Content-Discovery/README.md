# Content Discovery — TryHackMe

**Room:** Content Discovery  
**Platform:** TryHackMe  
**Objective:** Learn manual, automated and OSINT techniques to discover hidden content (admin pages, backups, old pages, config files, S3 buckets, etc.) and document findings.

---

## 1. Introduction: What is Content in Web Security?
Content can be any resource on a website — files, images, videos, backups, admin portals, or forgotten pages.  
Content discovery helps uncover resources that are not immediately visible but can expand the attack surface.  

Main approaches:
- **Manual discovery** — checking files like `robots.txt`, `sitemap.xml`, favicons, HTTP headers.  
- **Automated discovery** — using tools + wordlists to brute-force hidden paths.  
- **OSINT** — leveraging public information sources.  

---

## 2. Robots.txt
The `robots.txt` file instructs search engines on which pages should or should not be indexed.  
As penetration testers, this can reveal sensitive paths (e.g., `/admin`, `/backup`).  

📷 Screenshot:  
![Robots.txt](Screenshot%202025-09-11%20175334.jpg)
---

## 3. Favicon
Favicons are small icons in the browser tab.  
Sometimes default favicons remain from frameworks, which can fingerprint technologies in use.  

## 4. Sitemap.xml
Unlike `robots.txt`, the `sitemap.xml` file lists all pages the site owner *wants* indexed.  
This can reveal old or hidden pages.  

---

## 5. HTTP Headers
When making requests, servers respond with HTTP headers.  
These may expose server versions, frameworks, or scripting languages in use.  

📷 Screenshot:  
![Headers](Screenshot%202025-09-11%20182005.jpg)

---

## 6. Framework Stack
By analyzing favicons, headers, or page source, we can often identify the framework (e.g., WordPress, Laravel).  
Once identified, known vulnerabilities can be researched.  

---

## 7. External OSINT Resources
OSINT tools help gather information from public sources.  
Examples:  
- **Google Dorking** (`site:`, `filetype:`, `inurl:`)  
- **Wayback Machine** (historical pages)  
- **crt.sh** (SSL certificate data)  
- **GitHub search** (public repos with credentials or code)  

---

## 8. Wappalyzer
Wappalyzer is a browser extension/online tool that identifies website technologies (frameworks, CMS, analytics, payment processors, etc.).  

---

## 9. Wayback Machine
An internet archive that stores historical snapshots of websites.  
Helpful to uncover old but still active endpoints.  

---

## 10. GitHub
GitHub hosts public and private repositories.  
Searching for company names or domains may reveal exposed code, credentials, or configuration files.  

---

## 11. S3 Buckets
Amazon S3 allows public file hosting.  
Misconfigured S3 buckets may leak sensitive files.  
Format: `http(s)://{name}.s3.amazonaws.com`.  

---

## 12. Automated Discovery
Instead of manual checks, tools like **ffuf**, **dirb**, and **gobuster** use wordlists to brute-force hidden directories and files.  

📷 Screenshot:  
![Automated discovery](Screenshot%202025-09-11%20185113.jpg)

---

---

## Commands Used
```bash
# Get verbose headers
curl -v http://MACHINE_IP

# Directory brute-force examples
ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://MACHINE_IP/FUZZ
dirb http://MACHINE_IP/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
gobuster dir --url http://MACHINE_IP/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt


---
