we start with a pair of creds: judith.mader Password: judith09
nmap scan doesnt show anything interesting only SMB and normal DC ports
no custom shares on SMB, so i grab bloodhound files with bloodhound-python and immediately find an interesting vector

![[Pasted image 20251011160717.png]]

seems like i can escalate all the way to `CA_OPERATOR` from my current user, so im getting straight to it by abusing the `WriteOwner` on `Management` and adding myself as owner

![[Pasted image 20251011160946.png]]

After making myself owner, i give myself the option to add members
![[Pasted image 20251011161147.png]]

finally i can add myself to the group to elevate `Judith.Mader` privileges
![[Pasted image 20251011161306.png]]
now that i have `GenericWrite` i can use `pywhisker` to attempt to add shadow creds

![[Pasted image 20251011161529.png]]
i managed to add a credential to the user, now ill try to get a TGT using the generated PFX

![[Pasted image 20251011163641.png]]
i managed to get a TGT, but i couldnt authenticate using it in `Evil-WinRM` so i decided to use `getnthash.py` and the `AS-REP` key that i was given when generating the TGT
![[Pasted image 20251011163655.png]]

now that i got the NT hash, i can use it the reset the password of `CA_OPERATOR`
![[Pasted image 20251011163716.png]]
next thing i did is obviously run certipy (the box is called certified and my compromised user is called `CA_OEPRATOR` so its an obvious choice)
![[Pasted image 20251011172156.png]]
found a vulnerable template, i went to check certipy wiki to find out the prerequisites for this vulnerability
![[Pasted image 20251011172330.png]]

i checked and it seemed to me that this host is vulnerable, so i gave it a try
my victim account in this exploit is the `ca_operator`, since this is the account that can request the template, and i control its `SPN` as `management_svc` 

![[Pasted image 20251011172426.png]]
first step i changed the victim `SPN`

![[Pasted image 20251011172554.png]]
at this stage you need to obtain the victim credentials by adding shadow creds or doing a targeted kerberoast, whatever is possible to get a TGT of the victim, in my case i already changed his password to `asdasd123`

![[Pasted image 20251011170809.png]]

i can now export the TGT and request a certificate using my TGT as authenticating, leading me to receive `administrator.pfx`

![[Pasted image 20251011173212.png]]

now i need to change the victims `UPN` back to what it was before the exploit attempt

![[Pasted image 20251011170824.png]]

and finally, i can attempt to authenticate with certipy using the generated `PFX` 

![[Pasted image 20251011173507.png]]
i end up with the `Administrator NTLM hash`, it is now very simple to authenticate in whatever way available using pass the hash, in this case WinRM

![[Pasted image 20251011173647.png]]
