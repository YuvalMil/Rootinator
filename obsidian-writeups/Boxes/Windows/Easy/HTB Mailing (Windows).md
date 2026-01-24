nmap shows many ports but only http and smb are interesting
while enumerating http, i find that `download.php` is vulnerable to LFI, after looking online for a while about credential files for hmailserver, which was identified by nmap and also stated on the website, i find the file with the md5 hashed password
![[Pasted image 20251011060646.png]]
now the box creator expects us to assume that if there is a mail server running, it probably runs a client as well, and because it is windows, it might use the default window mail client which might be vulnerable to [CVE-2024-21413](https://github.com/xaitax/CVE-2024-21413-Microsoft-Outlook-Remote-Code-Execution-Vulnerability)
this vuln allows a NTLMv2 hash leak by forcing authentication of the SMTP server, using the POC and the credentials i got, i use responder to get the NTLMv2 hash for the user `maya` 
![[Pasted image 20251011061333.png]]
![[Pasted image 20251011061350.png]]
i cracked the NTLMv2 using hashcat, and got a foothold + user.txt
checking the program files directory, its possible to identify LibreOffice, which is an alternative tools suite to MSoffice, by checking the version and searching online, i find its a vulnerable version
![[Pasted image 20251011061643.png]]
[CVE-2023-2255](https://github.com/elweth-sec/CVE-2023-2255#cve-2023-2255) "allowed an attacker to craft a document that would cause external links to be loaded without prompt." 
for some unknown reason, the localadmin account is opening libreoffice files that appear in `C:\Important Documents\`
im not sure how i was supposed to figure it out, and the walkthrough doesnt answer that 
![[Pasted image 20251011062148.png]]
with the POC script, i create a malicious odt file, upload it to `C:\Important Documents\` and wait with my listener, after a few mins i get a revshell as localadmin, the Administrator account
![[Pasted image 20251011062250.png]]
