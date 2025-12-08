# **📡 Enumeration & Exploitation Walkthrough**

A structured, professional pentesting write-up formatted to match my GitHub documentation style.  
This walkthrough demonstrates **systematic enumeration**, **attack surface analysis**, **vulnerability identification**, and **ethical exploitation methodology**.

---

## **📡 Enumeration**

Initial enumeration began with a full TCP port sweep to map the system’s exposed services and identify viable attack vectors.

### **🔍 Full Nmap Scan**
```bash
nmap -sV -sC -A -T4 -p- 10.129.44.167 -oN nmap_scan.md
```

The scan provided key intelligence regarding:

- Open ports  
- Running services and versions  
- Default NSE script output  
- OS / service fingerprints  
- Potential misconfigurations  

---

## **📝 Key Findings from Nmap**

### **Open Ports**
(Add discovered ports here)

- Example: **22/tcp** – OpenSSH  
- Example: **80/tcp** – Apache HTTPD  
- Example: **445/tcp** – SMB File Sharing  

### **Service Insights**

- Version details  
- Possible associated CVEs  
- Outdated or vulnerable configurations  

### **Initial Attack Surface**

- Web service likely entry point  
- Potentially accessible SMB shares  
- SSH version worth noting  

---

## **🌐 Web Enumeration**

After confirming a running web service, additional enumeration was performed.

### **📂 Directory & File Discovery**
```bash
gobuster dir -u http://10.129.44.167 -w /usr/share/wordlists/dirb/common.txt -o gobuster.md
```

### **🧩 Tech Stack Fingerprinting**

- Framework identification  
- CMS / library versions  
- Interesting endpoints  
- Login panels or file upload mechanisms  

---

## **📁 SMB / Service Enumeration**

If SMB or additional auxiliary services were identified:

### **📄 SMB Share Listing**
```bash
smbclient -L //10.129.44.167/
```

### **📥 Mounting Anonymous Shares**
```bash
smbclient //10.129.44.167/sharename
```

### **🔍 Useful Artifacts**

- Credentials  
- Config files  
- Internal scripts  
- Backup archives  

---

## **🧩 Vulnerability Identification**

Based on enumeration:

- Version-based vulnerabilities detected  
- Possible CVEs identified  
- Misconfigurations observed  
- Privilege escalation paths mapped  

### **Potential Attack Vectors**

- Web injection attacks  
- File upload → RCE  
- Weak SMB permissions  
- Outdated libraries  
- Default credentials  

---

## **🚀 Exploitation**

Once a viable exploit path was confirmed:

### **🔧 Exploit Method Used**

- Describe the framework or exploit (manual, Metasploit, custom script, etc.)  
- Detail the logic behind the attack  
- Provide payload notes  

### **📟 Gaining Initial Access**
```bash
nc -lvnp 4444
```

Document shell stability, TTY upgrade, and post-access checks.

---

## **🔼 Privilege Escalation**

After establishing a foothold:

### **🧭 Local Enumeration**
```bash
linpeas.sh
```

### **Weaknesses Identified**

- SUID binaries  
- Misconfigured permissions  
- Credential reuse  
- Scheduled tasks  
- Kernel vulnerabilities  

### **📈 Privilege Escalation Path**

- Describe the method used  
- Include commands or scripts  
- Demonstrate final root access  

---

## **🏁 Post-Exploitation**

### **📦 Loot Collected**

- `user.txt`  
- `root.txt`  
- Credentials  
- System information  

### **🔄 Persistence**
Not used unless explicitly permitted.

---

## **🔒 Mitigation Recommendations**

- Patch outdated services  
- Enforce least-privilege principles  
- Remove world-writable configurations  
- Strengthen segmentation  
- Review credential policies  

---

## **📬 Final Notes**

This write-up is provided for **educational**, **ethical**, and **professional development** purposes.  
It demonstrates:

- Practical enumeration workflow  
- Clean documentation style  
- Realistic pentesting methodology  
- Reproducible technical steps  
