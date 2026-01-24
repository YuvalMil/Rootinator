starting with a set of credentials 

running nmap scan > no http, SMB not allowing guests. (can also see kerberos at 88)
edit hosts file and krb5.conf file after enumerating ldap with nmap | nmap -p 389 --script ldap-rootdse 10.10.11.69
with details obtained from nmap, generate a TGT for the compromised user, use TGT to authenticate to SMB
in SMB, got permissions to read and write on IT share
in IT share find a PDF that shows new vulnerabilities that need to be patched
read the description of the critical CVE's notice CVE 2025-24071, results in SMB authentication attempt to arbitrary server, leads to NTLM hash leak when ms-library file is extracted in windows file explorer
notice the IT share has zip archives and also the extracted content
attempt to upload a zip, can see it gets extracted automatically and then removed
download the python exploit from exploit-db, use it to generate the poison zip
attempt to catch the NTLM leak with impacket-smbserver, doesnt seem to work, probably doesnt support this authentication type
look for POC of this CVE on google, find a POC using Responder | sudo responder -I tun0 -v 
copy the hash, move to a file, crack with john, got clear creds for user p.agila
generate tgt with impacket for p.agila, use bloodhound-python to get bloodhound files remotely (tried to evil winRM with p.agila, doesnt work)
![[Pasted image 20250829173843.png]]
check route in bloodhound from p.agila to winrm_svc
p.agila can add itself to service accounts group, which has GenericWrite on winrm_svc
use bloodyAD to add p.agila to service accounts group
checked specterops website for info on how to abuse GenericWrite: 
With GenericWrite over a user, you can write to the “msds-KeyCredentialLink” attribute. Writing to this property allows an attacker to create “Shadow Credentials” on the object and authenticate as the principal using Kerberos PKINIT
specterops recommend using whisker, downloaded the python version, pyWhisker
with pyWhisker, add a pfx certificate to the KeyCredentialLink, pywhisker will give the key in output | pywhisker -d "fluffy.htb" -u "p.agila" -p "prometheusx-303" --target "winrm_svc" --action "add" --filename test1
![[Pasted image 20250829173111.png]]
it is now possible to generate a TGT by authenticating with the certificate that we planted in the account, can be done with PKINITtools (check pyWhisker repo for more details)
![[Pasted image 20250829173523.png]]
move tgt to Fluffy dir, export it to KRB5CCNAME, use evilWinRM to get a session with the generated TGT
grab user.txt from the desktop

Privilege escalation
couldnt find the way to DA on my own, used a write up:
https://medium.com/@debang5hu/fluffy-write-up-hackthebox-season-8-1fd0b3ff4adf