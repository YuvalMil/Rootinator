very easy machine, hardest part about it is figuring out there is smb access with no user, literally empty username and password
smb access leads to groups.xml file with cpassword for the user SVC_TGS, checked online and found a useful [tool](https://github.com/t0thkr1s/gpp-decrypt)
that decrypts the cpassword, so i now have access as SVC_TGS, i pulled bloodhound files with bloodhound python, found that its a very limited AD environment, so proceeding should be easy as there is barely anything to check, i checked Administrator user for SPN's and he does have "active/CIFS:445" so i request a TGS using impacket 
![[Pasted image 20251003152026.png]]
ccname2john didnt work, so i converted to kirbi using impacket-TicketConverter, and then used kirbi2john to crack it
![[Pasted image 20251003152123.png]]
and i easily get root.txt via SMB
![[Pasted image 20251003152142.png]]
