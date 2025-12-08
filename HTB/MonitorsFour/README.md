📡 Enumeration

Initial enumeration began with a full TCP port sweep to identify exposed services and map the system’s attack surface.

🔍 Full Nmap Scan
nmap -sV -sC -A -T4 -p- 10.129.44.167 -oN nmap_scan.md


The scan provided the following useful information:

Open ports

Service versions

Default NSE script results

OS / service fingerprints

Potential misconfigurations

📝 Key Findings from Nmap
Open Ports

(Add discovered ports here)

Example: 22/tcp (OpenSSH)

Example: 80/tcp (Apache)

Example: 445/tcp (SMB)

Service Insights

Version details

Potential CVEs

Outdated or vulnerable configurations

Initial Attack Surface

Web service likely entry point

SMB shares possibly accessible

SSH version worth noting

🌐 Web Enumeration

After identifying a web server, deeper enumeration was performed.

Directory & File Discovery
gobuster dir -u http://10.129.44.167 -w /usr/share/wordlists/dirb/common.txt -o gobuster.md

Tech Stack Fingerprinting

Frameworks detected

CMS / library versions

Interesting endpoints

Login portals or upload forms

📁 SMB / Service Enumeration

If SMB or other auxiliary services were identified:

SMB Share Listing
smbclient -L //10.129.44.167/

Mounting Anonymous Shares
smbclient //10.129.44.167/sharename

Useful Artifacts

Credentials

Config files

Scripts

Backup archives

🧩 Vulnerability Identification

Based on enumeration:

Detected possible CVEs

Identified version-based vulnerabilities

Located misconfigurations

Mapped privilege escalation paths

Potential Attack Vectors

Web injection

File upload / RCE

Weak SMB permissions

Outdated libraries

Default credentials

🚀 Exploitation

Once the viable vector was confirmed:

Exploit Method Used

Describe framework or exploit (manual or scripted)

Logic behind the attack

Payload details

Gaining Initial Access
nc -lvnp 4444


Document shell access, stability, TTY upgrade, etc.

🔼 Privilege Escalation

After achieving a foothold:

Local Enumeration
linpeas.sh

Weaknesses Identified

SUID binaries

Misconfigured permissions

Credential reuse

Scheduled tasks

Kernel vulnerabilities

Privilege Escalation Path

Describe the escalation technique used

Provide commands or scripts

Show final root access

🏁 Post-Exploitation
Loot Collected

user.txt

root.txt

Credentials

System information

Persistence (If applicable)

Not used, unless explicitly allowed

🔒 Mitigation Recommendations

Patch outdated services

Enforce least privilege

Remove world-writable configs

Improve segmentation

Review credential policies

📬 Final Notes

This write-up is provided for educational, ethical, and professional development purposes.
It demonstrates:

Practical enumeration workflow

Clean documentation style

Realistic pentesting methodology

Reproducible technical steps
