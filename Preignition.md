# 🛡️ HTB — Preignition Security Analysis

> Practical walkthrough covering reconnaissance, web enumeration, hidden content discovery, and weak authentication exploitation.

---

## 📌 Lab Information

| Property | Value |
|-----------|--------|
| 🏢 Platform | Hack The Box |
| 💻 Machine | Preignition |
| 📂 Category | Web Security |
| 🎯 Focus Area | Authentication Weakness |
| 🛠 Difficulty | Very Easy |

---

# 🎯 Objective

Identify exposed services, discover hidden content, analyze authentication mechanisms, and understand how weak/default credentials can lead to unauthorized access.

---

# 🔎 Reconnaissance

## 🌐 Full Port Scan

### Command

```bash
nmap -v -Pn -p- --min-rate 10000 -T4 TARGET_IP
```

### Result

```text
PORT   STATE SERVICE
80/tcp open http
```

### Observation

✅ Only one service exposed

✅ HTTP identified as attack surface

---

## 🔬 Service Enumeration

### Command

```bash
nmap -sC -sV -p80 TARGET_IP
```

### Result

```text
PORT   STATE SERVICE VERSION
80/tcp open http nginx 1.14.2

http-title: Welcome to nginx!
```

### Observation

✅ nginx service detected

⚠ Default page shown

⚠ Real application likely hidden

---

# 🌍 Initial Web Investigation

### Command

```bash
curl http://TARGET_IP
```

### Result

```html
Welcome to nginx!
```

### Observation

❌ No visible functionality

❌ No application links exposed

✅ Continue enumeration

---

# 📁 Content Discovery

### Command

```bash
gobuster dir \
-u http://TARGET_IP \
-w /usr/share/wordlists/dirb/common.txt
```

### Result

```text
admin.php (Status: 200)
```

### Discovery

```text
http://TARGET_IP/admin.php
```

### Observation

🚨 Hidden administrative interface discovered

---

# 🔐 Authentication Analysis

Administrative login page contained:

- 👤 Username field
- 🔑 Password field

Security observations:

❌ No MFA

❌ No CAPTCHA

❌ No account lockout

❌ No brute-force protection

❌ No visible CSRF protection

---

# ⚔ Authentication Testing

### Request

```http
POST /admin.php HTTP/1.1

username=admin&password=admin
```

### Result

```text
Authentication Successful
```

### Observation

🚨 Weak/default credentials allowed access

---

# 🚨 Security Finding

## Weak Authentication Controls

### Description

Administrative access was possible through weak/default credentials.

### Impact

An attacker could gain unauthorized administrative access without exploiting software vulnerabilities.

### Risk

🔴 High

---

# 🧠 Attack Flow

```text
Nmap Scan
    ↓
HTTP Service Discovery
    ↓
Default nginx Page
    ↓
Directory Enumeration
    ↓
admin.php Discovery
    ↓
Authentication Testing
    ↓
Weak Credentials Successful
    ↓
Administrative Access
```

---

# 🎯 MITRE ATT&CK Mapping

| Technique | ATT&CK ID |
|------------|------------|
| Valid Accounts | T1078 |
| Brute Force | T1110 |

---

# 🛡️ Defender Recommendations

✅ Remove default credentials

✅ Enforce strong password policies

✅ Enable MFA

✅ Restrict admin interfaces

✅ Add account lockout

✅ Implement rate limiting

✅ Monitor failed login attempts

---

# 💡 Key Learning

> Not every compromise starts with a sophisticated exploit.

> Sometimes attackers simply discover exposed administrative interfaces protected by weak credentials.

---

# 🛠 Tools Used

- Nmap
- Gobuster
- Burp Suite
- Curl
- Firefox

---

# 📚 Skills Practiced

✔ Reconnaissance

✔ Service Enumeration

✔ Directory Discovery

✔ Authentication Testing

✔ Security Analysis

✔ Defensive Thinking

---

## ⚠ Disclaimer

This walkthrough is created for educational purposes using authorized lab environments only.
