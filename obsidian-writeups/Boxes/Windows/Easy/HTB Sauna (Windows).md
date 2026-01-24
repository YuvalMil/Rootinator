starting enum with nmap, typical DC with http running too
enumerating HTTP, found nothing interesting - theres a section in the website to introduce the team, i can copy these names and run it with a usernames wordlist generator script to get a list of usernames to throw at impacket-getNPUsers
found a user without preauth, impacket grabbed a hash from the tgt - i now need to attempt to crack it
![[Pasted image 20251017214607.png]]
![[Pasted image 20251017214637.png]]
now with foothold i can grab bloodhound files and get user.txt
winPEAS found another set of creds
![[Pasted image 20251017214802.png]]
this user has the permissions to perform a DCSync
![[Pasted image 20251017214850.png]]

![[Pasted image 20251017220710.png]]
![[Pasted image 20251017220725.png]]