# Subdomain Enumeration — TryHackMe

**Room:** Subdomain Enumeration  
**Platform:** TryHackMe  
**Objective:** Learn different techniques (OSINT, brute force, virtual hosts) to enumerate subdomains and expand the attack surface.

---

## 1. Introduction: Why Enumerate Subdomains?
Subdomain enumeration is the process of discovering valid subdomains for a domain.  
We do this to **expand our attack surface** and identify additional points of vulnerability.  

Three main methods explored in this room:  
- **Brute Force**  
- **OSINT (Open-Source Intelligence)**  
- **Virtual Host Enumeration**  

---

## 2. OSINT — SSL/TLS Certificates
When Certificate Authorities issue SSL/TLS certificates, they log them in **Certificate Transparency (CT) logs**.  
These logs are public and can be searched to reveal subdomains associated with a domain.  

🔗 Example: [crt.sh](https://crt.sh)  
- Querying `tryhackme.com` shows historical and current certificate entries.  
- From the logs, we can identify valid subdomains.  

📷 Screenshot:  
![crt.sh search](Screenshot%202025-09-17%20041658.jpg)

---

## 3. OSINT — Search Engines
Search engines can reveal indexed subdomains using advanced filters.  
Example query:
site:*.tryhackme.com -site:www.tryhackme.com

This lists subdomains of `tryhackme.com` but excludes `www.tryhackme.com`.  

---

## 4. Brute-Force DNS Enumeration
Brute forcing subdomains means trying large lists of common names against DNS records.  
Tools like **dnsrecon** automate this process.  

In the lab, we used `dnsrecon` to enumerate possible subdomains from a wordlist.  

📷 Screenshot:  
![dnsrecon brute force](Screenshot%202025-09-17%20041901.jpg)

---

## 5. Automated OSINT — Sublist3r
Sublist3r is a tool that automates subdomain discovery by combining search engines, certificate logs, and other OSINT sources.  

In this room, running Sublist3r revealed additional subdomains not seen in manual searches.  

📷 Screenshot:  
![Sublist3r results](Screenshot%202025-09-17%20042031.jpg)

![Sublist3r results](Screenshot%202025-09-17%20042109.jpg)

---

## 6. Virtual Host Enumeration
Not all subdomains are public. Some are internal or hidden (e.g., dev, staging, admin).  
They may not appear in DNS but can still exist on the same server.  

### Technique: Host Header Injection
We modify the **Host header** in HTTP requests to test for hidden subdomains.  
Example command with `ffuf`:
```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt \
     -H "Host: FUZZ.acmeitsupport.thm" \
     -u http://MACHINE_IP

Since this produces many valid responses, we filter by page size using the -fs switch:

ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt \
     -H "Host: FUZZ.acmeitsupport.thm" \
     -u http://MACHINE_IP -fs {size}

This revealed two valid hidden subdomains in the lab.

---

## Virtual Host Enumeration
📷 Screenshot:  
![virtual host](Screenshot%202025-09-11%20195202.jpg)


---
