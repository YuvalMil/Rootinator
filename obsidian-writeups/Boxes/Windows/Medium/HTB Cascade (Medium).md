starting enum with nmap, target is a classic DC, i enum smb and find nothing - i start ldap and rpc enum - also cant find anything tried whatever i could think of, eventually checked guided mode and found out that a certain user has a special field in his ldap records `cascadeLegacyPwd` and it contains the user password in base64
![[Pasted image 20251019164512.png]]
with my first set of creds, i get bloodhound files to have a better look on the domain, and i start smb enum again, this time i have access to `data` share, and i download pretty much anything in there that i think could hold a hint
first thing i find is a mail, and it contains important info
![[Pasted image 20251019164751.png]]
since i have bloodhound files already, i know that this user doesnt exist right now, so its most likely deleted already. but now i know that getting `Tempadmin` 's password is likely to be a vector
the next important thing i find in this SMB share is a `VNC Install.reg` file, in a directory of the user `s.smith` and in that file, theres a field for `password` but its encrypted / encoded in some way
![[Pasted image 20251019165328.png]]

after some googling, i found this very convenient 1liner
![[Pasted image 20251019165406.png]]
i just replace the commands hex with my hex, and run it
![[Pasted image 20251019165502.png]]
![[Pasted image 20251019165523.png]]
gaining access to `s.smith` is showing a new SMB share called `audit$` and in there i find 2 interesting files and a db file. 
`CascAudit.exe` and `CascCrypto.dll` because the files name has "Casc" i assume these are custom files made for the box, so i download both to my host and open them in VScode, where i have a decompiler extension installed, looking around in the decompiled code, i find exactly what i want.
first, in the decompiled DLL, i find 2 functions, 1 for encrypting and 1 for decrypting, and they contain much needed info, the type of encryption used in the binary, as well as some of the variables
![[Pasted image 20251019165957.png]]
checking the decompiled version of the exe file, i find the secret key and the code that describes where the encrypted password is stored

![[Pasted image 20251019170135.png]]

i see that the value is stored in a table called `LDAP`, i open the db file that i downloaded using sqlite3, and check for content in the table, theres only 1 row

![[Pasted image 20251019170526.png]]
now i have all i need to get the `ArkSvc` user password, i found a working online decryptor that i used
![[Pasted image 20251019170629.png]]
![[Pasted image 20251019170657.png]]
now since this user is the only one in the group `AD Recycle Bin` i thought my way forward would be restoring the deleted domain admin account, but i was missing permissions to do that.
i ran `Get-ADObject -filter 'isDeleted -eq $true' -includeDeletedObjects -Properties *` to get a better picture of the deleted objects in the domain, and i found something that i didnt expect
![[Pasted image 20251019171002.png]]
its the same custom field that contained the user's password in base64 for `r.thompson`
after decoding i check with NXC
![[Pasted image 20251019171119.png]]
