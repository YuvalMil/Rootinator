start with nmap, note SMB, and HTTP, also kerberos and ldap
check for SMB guest or anon login, doesnt work
check HTTP, shows setting page for printer user
wont let you edit password or username, checking the request in burp shows that the form is actually only sending the IP or hostname specified
open a listener at 389, change the host on the form to tun0 IP, submit
listener should receive the password for the svc-printer user
run bloodhound-ce-python with credentials, it seems svc-printer has remote management user privileges
use NTLM auth with evil WinRM
grab user.txt 
look at bloodhound results, start with checking compromised user groups
notice 'SERVER OPERATORS'
read about the group privileges and priv esc vectors online
POC shows simple service binary exploitation, generating msfvenom exe payload, replacing binpath for a service i can control, open listener and restart service
generated payload with msfvenom in exe format, uploaded using Evil-WinRM, replaced the VMTools binpath with my payload
sc.exe stop VMTools
sc.exe start VMTools
listener received shell as nt authority
grab root.txt
