## Enumeration

We begin with full TCP port and service enumeration using Nmap.

    nmap -sV -sC -A -T4 -p- 10.10.11.92

### Results Summary
- 22/tcp – OpenSSH 8.9p1 (Ubuntu)
- 80/tcp – Apache 2.4.52  
  Redirects to http://conversor.htb/

The scan confirms a Linux-based host running a web service and SSH. Based on the HTTP redirect, the hostname is added to /etc/hosts.

    10.10.11.92 conversor.htb

---

## Web Application Access

Navigating to http://conversor.htb presents a login page. Testing default credentials (admin:admin) results in a successful login, granting administrative access.

The application allows users to upload:
- An Nmap XML output
- A corresponding XSLT stylesheet

The service processes the XML using the supplied XSLT and renders the output as HTML.

---

## Application Reconnaissance

The About page discloses internal staff information:
- FisMatHack – Backend Developer
- Arturo Vidal – Frontend & UX
- David Ramos – Team Lead

A support email is listed as contact@conversor.htb, suggesting the email format firstname@conversor.htb.

The application provides an option to download the source code for review.

---

## Source Code Review

Reviewing app.py reveals the following hardcoded database path:

    DB_PATH = '/var/www/conversor.htb/instance/users.db'

This indicates where user credentials are stored.

---

## Directory Enumeration

Web directory enumeration is performed using Gobuster.

    gobuster dir -u http://conversor.htb/ -w /usr/share/wordlists/dirb/common.txt

### Discovered Endpoints
- /about
- /login
- /logout
- /register
- /javascript (403)
- /server-status (403)

---

## XSLT Injection and Remote Code Execution

The upload functionality processes user-supplied XSLT server-side, making it vulnerable to XSLT injection.

Using a payload from PayloadsAllTheThings, a malicious XSLT stylesheet is uploaded that writes a Python script to:

    /var/www/conversor.htb/scripts/

This provides access to users.db, from which FisMatHack’s credentials are extracted.

---

## Initial Access

Using the recovered credentials, SSH access is obtained.

    ssh fismathack@conversor.htb

The user flag (user.txt) is accessible at this stage.

---

## Privilege Escalation

Local enumeration reveals that the user fismathack can execute needrestart with sudo privileges.

The binary is vulnerable to CVE-2024-48990. A public proof-of-concept is used to exploit this vulnerability and escalate privileges to root.

---

## Root Access

With root privileges obtained, the final flag is retrieved.

    cat /root/root.txt
