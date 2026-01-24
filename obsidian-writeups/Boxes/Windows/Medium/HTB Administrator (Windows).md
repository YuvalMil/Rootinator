i start with a set of creds - Username: Olivia Password: ichliebedich
nmap shows ftp, smb, few more ports less interesting, after checking SMB and rid brute forcing i use bloodhound-python to get some more info about the domain
turn out my user has `GenericAll` on `Michael@Administrator.HTB`
![[Pasted image 20251011081544.png]]
`net rpc password "michael" "asdasd123" -U "ADMINISTRATOR.HTB"/"Olivia"%"ichliebedich" -S "10.129.183.74"`
Now it seems that `Michael` has `ForceChangePassword` on `Benjamin` 
![[Pasted image 20251011082530.png]]
`net rpc password "benjamin" "asdasd123" -U "ADMINISTRATOR.HTB"/"michael"%"asdasd123" -S "10.129.183.74"`
i find that `Benjamin` has a password safe file on his FTP share, i download it and try to crack it with john (converted it with psw2john)
![[Pasted image 20251011082924.png]]
i open the safe and check the creds with `pwsafe backup.psafe3`
it has creds for 3 users, `alexander` `emily` and `emma`, i find that emily is a strong user with `GenericWrite` on `Ethan` which has `DCSync` permission, meaning i can use the `GenericWrite` to attempt to add shadow creds or do a targeted kerberoast, and in the case it works, ill get domain admin easily
first i tried to use pywhisker, it seemed to work at first
![[Pasted image 20251011083707.png]]
but the server didnt allow this kind of PADATA
![[Pasted image 20251011083738.png]]
i tried to remove the password from the pfx cert using certipy, and then to convert the pfx file to PEM cert and key, but the outcome was the same
![[Pasted image 20251011083904.png]]
i decided to use a targeted kerberoasting attack, and used a tool called `targetedKerberoast.py`, it automatically adds a SPN to available targets using the provided creds, and then performs a kerberoast attack on the added SPN
![[Pasted image 20251011084137.png]]
i copied the hash to a file and ran john, it was cracked instantly
![[Pasted image 20251011084214.png]]
now with ethan's cleartext password, its possible to perform a DCsync
![[Pasted image 20251011084251.png]]
for the DCsync i can use `impacket-secretsdump`
![[Pasted image 20251011084331.png]]
![[Pasted image 20251011084409.png]]
