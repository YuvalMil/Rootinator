---
layout: writeup
title: Pelican
platform: Proving Grounds
difficulty: Medium
box_icon: /assets/writeups/icons/Pelican.png
date: 2025-10-28
tags:
  - zookeeper
  - exhibitor-web-ui
  - rce
  - gcore
  - memory-dump
  - sudo
os: Linux
ip: 192.168.x.x
---
<link rel="stylesheet" href="{{ '/assets/css/obsidian-dividers.css' | relative_url }}">

## Summary

<div class="divider divider-info">
    <span class="divider-title">TL;DR</span>
    <span class="divider-content">Pelican is a Medium Linux box running an outdated ZooKeeper installation with an exposed Exhibitor Web UI vulnerable to remote code execution. After gaining initial access, privilege escalation involves using sudo permissions on gcore to dump process memory and extract root credentials from a running process.</span>
</div>

**Key Vulnerabilities:**
- Outdated ZooKeeper Exhibitor Web UI (built 2014)
- RCE in Exhibitor configuration functionality
- Sudo permissions on gcore for memory dumping
- Credentials stored in process memory

---

## Enumeration

### Nmap Scan

**Initial scan:**
```bash
nmap -vv -T5 -p- 192.168.x.x

nmap -vv -T5 -p22,139,445,2181,2222,8080,8081 -sC -sV 192.168.x.x
```

**Results:**

| Port | Service        | TCP/UDP |
| ---- | -------------- | ------- |
| 22   | SSH            | TCP     |
| 139  | Netbios        | TCP     |
| 445  | SMB            | TCP     |
| 2181 | ZooKeeper      | TCP     |
| 2222 | SSH (alt)      | TCP     |
| 8080 | HTTP           | TCP     |
| 8081 | HTTP (redir)   | TCP     |

**Key findings:**
- Multiple HTTP services on different ports
- Two SSH services (ports 22 and 2222)
- SMB available for enumeration
- ZooKeeper service exposed

---

### SMB Enumeration

Quick SMB check confirmed no immediate access or useful shares.

---

### HTTP Enumeration

**Step 1:** Detailed nmap scan reveals interesting information

![Pasted image 20251028195403.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195403.png)

HTTP service on port 8081 redirects to Exhibitor endpoint at port 8080.

**Step 2:** Explore the Exhibitor Web UI

![Pasted image 20251028195507.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195507.png)

<div class="divider divider-info">
    <span class="divider-title">ZooKeeper & Exhibitor</span>
    <span class="divider-content">Apache ZooKeeper is a distributed coordination service used by many applications. Exhibitor is a web-based UI and monitoring tool for ZooKeeper clusters. It provides configuration management and operational visibility but can be vulnerable if exposed without proper authentication.</span>
</div>

Application identified as **ZooKeeper Exhibitor Web UI**.

**Step 3:** Check version information

![Pasted image 20251028195601.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195601.png)

Build date: **2014** (11 years old!) - highly likely to have vulnerabilities.

---

## Initial Foothold

### Vulnerability Discovery

**Vulnerability:** Exhibitor Web UI Remote Code Execution

Searching for ZooKeeper Exhibitor exploits revealed:

[Exhibitor Web UI 1.7.1 - Remote Code Execution - ExploitDB](https://www.exploit-db.com/exploits/48654)

![Pasted image 20251028195719.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195719.png)

<div class="divider divider-warning">
    <span class="divider-title">Exhibitor RCE</span>
    <span class="divider-content">The Exhibitor Web UI allows modification of ZooKeeper configuration through a web interface. By injecting malicious commands into configuration fields (such as java.env or log4j settings), attackers can achieve remote code execution when the configuration is applied. This vulnerability exists due to insufficient input validation and sanitization.</span>
</div>

---

### Exploitation

**Step 1:** Use the Exhibitor RCE exploit

![Pasted image 20251028195752.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195752.png)

![Pasted image 20251028195802.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195802.png)

<div class="divider divider-root">
    <span class="divider-title">Shell Access</span>
    <span class="divider-content">Successfully obtained reverse shell as user charles</span>
</div>

---

## User Flag

**Step 2:** Retrieve user flag

```bash
cat /home/charles/local.txt
```

Flag obtained!

---

## Privilege Escalation

### Sudo Enumeration

**Step 1:** Check sudo permissions

![Pasted image 20251028195854.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195854.png)

```bash
sudo -l
```

User charles can run `/usr/bin/gcore` as root without password!

<div class="divider divider-info">
    <span class="divider-title">gcore Utility</span>
    <span class="divider-content">gcore is a utility that generates core dumps of running processes. A core dump is a snapshot of process memory at a specific point in time, containing all data held in memory including potentially sensitive information like credentials, keys, and other secrets. When run with sudo, it can dump memory from any process, including those running as root.</span>
</div>

---

### GTFOBins Research

**Step 2:** Check GTFOBins for gcore exploitation

![Pasted image 20251028195914.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028195914.png)

GTFOBins confirms gcore can be used to read process memory, potentially exposing sensitive data.

---

### Process Memory Dump

**Step 3:** Identify root processes

![Pasted image 20251028200036.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028200036.png)

```bash
ps aux | grep root
```

Multiple processes running as root - any could contain credentials in memory.

**Step 4:** Dump process memory with gcore

```bash
# Identify interesting process PID
ps aux | grep root

# Dump process memory (example PID: 485)
sudo /usr/bin/gcore 485
```

**Step 5:** Extract strings from memory dump

![Pasted image 20251028200228.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028200228.png)

```bash
strings core.485 | less
```

Search through the strings output for credentials.

---

### Credential Discovery

**Step 6:** Find root credentials in dump

![Pasted image 20251028200303.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028200303.png)

![Pasted image 20251028200328.png](/assets/images/2025-10-28-Pelican/Pasted image 20251028200328.png)

Found root password in process memory dump!

<div class="divider divider-root">
    <span class="divider-title">Root Password Found</span>
    <span class="divider-content">Successfully extracted root credentials from process memory</span>
</div>

**Step 7:** Switch to root

```bash
su root
# Enter password found in memory dump
```

<div class="divider divider-root">
    <span class="divider-title">Root Access</span>
    <span class="divider-content">Successfully escalated to root user</span>
</div>

---

## Root Flag

```bash
cat /root/proof.txt
```

Root flag obtained!

---

## Post-Exploitation

**Flags:**
- User: `/home/charles/local.txt`
- Root: `/root/proof.txt`

**Attack Chain Summary:**
1. Nmap reveals multiple HTTP services and ZooKeeper
2. Exhibitor Web UI discovered on port 8080
3. Build date (2014) indicates outdated version
4. Exhibitor RCE vulnerability identified (ExploitDB 48654)
5. RCE exploited to obtain shell as charles
6. User flag retrieved
7. Sudo permissions reveal gcore can be run as root
8. Root processes identified via ps
9. Process memory dumped using sudo gcore
10. Strings extracted from core dump
11. Root password discovered in process memory
12. Successfully switched to root user
13. Root flag obtained

**Key Lessons:**
- Outdated software versions (especially from 2014) are critical targets
- Exhibitor Web UI should never be exposed without authentication
- Configuration interfaces can be abused for RCE
- Sudo permissions on memory dumping tools (gcore, gdb) are dangerous
- Process memory often contains sensitive data like passwords
- Core dumps preserve complete process state including credentials
- Always check GTFOBins for sudo binary exploitation methods
- Strings analysis of memory dumps can reveal plaintext credentials
- Services with configuration modification capabilities require strict access controls

---

## References

- [Exhibitor Web UI RCE - ExploitDB 48654](https://www.exploit-db.com/exploits/48654)
- [Apache ZooKeeper - Official](https://zookeeper.apache.org/)
- [Netflix Exhibitor - GitHub](https://github.com/soabase/exhibitor)
- [GTFOBins - gcore](https://gtfobins.github.io/gtfobins/gcore/)
- [Core Dump Analysis](https://www.kernel.org/doc/html/latest/admin-guide/sysctl/kernel.html#core-pattern)
- [Process Memory Forensics](https://www.sans.org/white-papers/36532/)

---

## Timeline

```mermaid
graph LR
    A[Nmap Scan] --> B[HTTP Enum]
    B --> C[Exhibitor UI]
    C --> D[Version Check]
    D --> E[RCE Exploit]
    E --> F[Charles Shell]
    F --> G[Sudo Check]
    G --> H[gcore Binary]
    H --> I[Memory Dump]
    I --> J[Strings Analysis]
    J --> K[Root Password]
    K --> L[Root Shell]
```

---

**Pwned on:** 28/10/2025

**Difficulty Rating:** ⭐⭐⭐ (Memory dump analysis is unique)  
**Fun Factor:** ⭐⭐⭐⭐ (Excellent introduction to process memory exploitation)
