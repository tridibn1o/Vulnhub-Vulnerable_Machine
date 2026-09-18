# Mr. Robot — VulnHub Write-up

### Machine Information

| **Machine** | Mr. Robot: 1 |
| **Platform** | VulnHub |
| **Difficulty** | Intermediate |
| **Attacker OS** | Kali Linux |
| **Environment** | Isolated VirtualBox Lab (Host-Only / NatNetwork) |
| **Goal** | Obtain all three keys and achieve root-level access |

---

### Overview

This write-up documents the complete compromise of the **Mr. Robot: 1** VulnHub machine in an isolated, authorized lab environment. 

The assessment demonstrates an end-to-end penetration-testing workflow, beginning with host discovery and service reconnaissance, continuing through credential brute-forcing, reverse shell access, and shell stabilization, and culminating in local privilege escalation via SUID binary abuse.

---

### Key Findings

* **Information Disclosure:** `robots.txt` exposed a sensitive dictionary file (`fsocity.dic`) and the first system flag (`key-1-of-3.txt`).
* **WordPress Authentication Weaknesses:** Login error messaging enabled user enumeration (`elliot`), and the absence of rate limiting permitted offline/online dictionary attacks.
* **Arbitrary PHP Code Execution:** Unrestricted WordPress theme editing allowed injecting a PHP reverse shell via `404.php`.
* **Insecure Credential Storage:** An unshadowed MD5 password hash for local user `robot` was stored with world-readable permissions.
* **Privilege Escalation via SUID Abuse:** Outdated `/usr/local/bin/nmap` (v3.81) retained the SUID root bit, permitting arbitrary root shell escape via interactive mode.

---

### Methodology Flow

```text
Reconnaissance (Host Discovery & Port Scanning)
      ↓
Web Enumeration (robots.txt & Dictionary Recovery)
      ↓
WordPress Exploitation (User Enumeration & Brute-Force)
      ↓
Initial Access (Reverse Shell via Theme Editor)
      ↓
Shell Stabilization (Python PTY Upgrade)
      ↓
Horizontal Privilege Escalation (MD5 Hash Cracking -> user 'robot')
      ↓
Vertical Privilege Escalation (SUID Nmap Interactive Escape -> root)
      ↓
Flag Collection & Post-Exploitation
