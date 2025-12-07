# CTF Writeups

Clean, structured walkthroughs for HTB, TryHackMe, and various CTF challenges.  
These notes focus on methodology, enumeration flow, exploitation paths, and key takeaways to help reinforce practical offensive security skills.

---

## Overview

This repo stores writeups for:
- **HackTheBox**
- **TryHackMe**
- **Standalone CTFs**
- **Practice labs / boot2root machines**

All content is written to highlight:
- Clear attack paths  
- Commands used at each stage  
- Lessons learned  
- Remediation or hardening notes  

No flags, passwords, or challenge spoilers are included.

---

## Example Enumeration Flow

```text
1. Recon  
   - nmap scan  
   - service enumeration  
   - tech-stack fingerprinting  

2. Attack Surface
   - version analysis  
   - potential CVEs  
   - misconfigurations  

3. Exploitation  
   - foothold  
   - lateral movement (if applicable)

4. PrivEsc  
   - kernel, sudo, PATH, capabilities, services  

5. Wrap-Up  
   - mitigation notes  
