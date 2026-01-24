starting enum with nmap, typical DC with ldap smb rpc winrm
guest is disabled, no smb access with null auth, ldap lets me query everything with null auth, so i get a lot of info
checked users descriptions, and user / admin comments, found nothing, running impacket-GetNPUsers with null auth finds a user without preauth
![[Pasted image 20251017163903.png]]
running the same impacket script with the discovered username issues a TGT request and returns a hash i can attempt to crack with john
![[Pasted image 20251017164000.png]]
hash is cracked and i get a set of creds
![[Pasted image 20251017164020.png]]
i can quickly use NXC to check if WinRM access for this user is possible
![[Pasted image 20251017164048.png]]
now i got a foothold, and i can also get bloodhound files for further exploitation
i notice my svc account has a lot of permissions
![[Pasted image 20251017164200.png]]

i check for direct route from my owned user to the administrator account, and i find a route

![[Pasted image 20251017164259.png]]

it means i can add svc-alfresco to `exchange windows permissions` and then add the right to perform a DCsync, that should lead to DA access.
first i add `svc-alfresco` to the group 
![[Pasted image 20251017164420.png]]

now i want to add dcsync rights over the domain

![[Pasted image 20251017164442.png]]

now i just run impacket-secretsdump with my set of creds
![[Pasted image 20251017164513.png]]
got the administrator NTLM, now its just WinRM and grab the flag
![[Pasted image 20251017164543.png]]
