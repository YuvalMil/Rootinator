host is looking like a typical DC, starting enum with SMB and checking for guest access or null auth access
![[Pasted image 20251030171828.png]]
there is no guest or null access to SMB, but null auth is enabled, so i can try a few impacket scripts
started with `impacket-samrdump`
![[Pasted image 20251030171920.png]]
this looks like it could be the user's password, can validate with nxc
![[Pasted image 20251030171950.png]]
user creds are valid, and he has read access to `Password Audit`
in the share i find a `sam` `system` hives, as well as `ntds.dit`, and i use `impacket-secretsdump` to parse the files
![[Pasted image 20251030155536.png]]

i tried the Administrator's hash and it didnt work, so i started trying other users, all the users had their password expired except for `L.Livingstone`
![[Pasted image 20251030155556.png]]
at this point i decided to grab `Bloodhound` files to get a better look at permissions on the domain
![[Pasted image 20251030172314.png]]
seems that the compromised user has `GenericAll` on the DC, i first tried to perform shadow credential attack
![[Pasted image 20251030172417.png]]
it didnt work, the KDC doesnt support this auth type

![[Pasted image 20251030172500.png]]
i moved to a different attack, `Resource-Based Constrained Delegation`
i simply followed the steps from Bloodhound and `The Hacker Recipes`
First is adding a computer account / using a controlled account with a SPN set, and adding a delegation permission to the object i have `GenericAll` on.
![[Pasted image 20251030155510.png]]
now i need to get a ST, i had some issues with this part because i wasnt sure which SPN i need to choose, but instead of choosing from the list of SPN's, what worked for me was just using `cifs/computername.domain.local`

![[Pasted image 20251030171542.png]]
here i lost time because i thought i should be able to run `impacket-secretsdump` with the ST, not only i couldnt, theres no need for it since i can just psexec with the right syntax (it wouldnt work for a few minutes because i didnt specify the DC after the @)

![[Pasted image 20251030171650.png]]