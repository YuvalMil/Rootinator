run nmap, note smb, ldap, kerberos, DNS, target is likely a DC
enum SMB shares with guest account, find the trainees share
read important.txt, and bruteforce RID using guest account to find more users
find the trainee user, which is mentioned in the txt file
bruteforce user over ldap with rockyou, the password is also trainee
check smb shares with trainee user, find new share notes
get flag 1 from notes, read the txt
txt mentions a pre-windows-2000 machine, which is known for being vulnerable because their password is by default the account name
brute force RID with the trainee account, find machine account BANKING$
try to authenticate to SMB with the machine account, get notorious error NT_STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT
this is still useable if we can change the machine account password
use kpasswd to change password and use the machine account to run certipy-ad find
note the CA CN, and also note vulnerable template (ESC1)
use Certipy to request the certificate with the machine user but impersonating Administrator
![[Pasted image 20250831204526.png]]
after generating the certificate, use certipy auth to authenticate with the generated certificate
![[Pasted image 20250831204713.png]]
used the NTLM hash for passthehash with PsExec.py, as SMB is open and psexec allows for passthehash authentication
grab the root flag, you have system priv
