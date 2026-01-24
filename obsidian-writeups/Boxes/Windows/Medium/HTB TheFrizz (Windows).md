target is a classic dc which is also running HTTP, NTLM auth seems disabled, nothing to get from ldap or smb so i move to http enum
i notice a login button that leads to a user portal
![[Pasted image 20251025160740.png]]
this login portal also reveals the running application and the version number
![[Pasted image 20251025160803.png]]
this version is vulnerable to RCE via file upload, used this [exploit](https://github.com/davidzzo23/CVE-2023-45878)  to get a revshell
i get a foothold as w.webservice
immediately start checking the application files to look for potential credentials / secrets. i found the DB creds in config.php
![[Pasted image 20251025161036.png]]
i open a chisel tunnel and use the tunnel connection to connect to the mysql server, for some reason localhost fails but 127.0.0.1 works
![[Pasted image 20251025161234.png]]
i find the table `gibbonperson` and it contains 1 user
![[Pasted image 20251025161312.png]]
now cracking this hash is a bit tricky, the best way to do it is look in gibbon repo and find the part that describes the password hashing
it seems that the hashing configuration is hashing $salt and then $password
![[Pasted image 20251025161535.png]]
if using john, dynamic_61 is the right format
![[Pasted image 20251025161632.png]]
![[Pasted image 20251025161653.png]]
i can confirm the creds with nxc, but what i want is to generate a tgt and login with ssh
![[Pasted image 20251025161803.png]]
now i have access as f.frizzle, in the recycle bin, there are 2 folders, 1 of the folders is named after the SID of `f.frizzle`, when i looked in there as the webservice user it was empty, but now that i am `f.frizzle` i might be able to see the contents
![[Pasted image 20251025162244.png]]
![[Pasted image 20251025162316.png]]
in that folder i found 2 7z files, i download both to my host by placing them in the web root directory
then i use `p7zip` to decompress the files
![[Pasted image 20251025162427.png]]
its a `wapt` directory
![[Pasted image 20251025162445.png]]
in `wapt/conf/waptserver.ini` i find some secrets
![[Pasted image 20251025162525.png]]
this base64 encoded password might work for a different use in the domain
![[Pasted image 20251025162621.png]]
![[Pasted image 20251025162641.png]]
now i have access to `M.SchoolBus`, looking at my BloodHound graph, it appears this user has interesting privileges
![[Pasted image 20251025162749.png]]

in addition to these permissions, this user is a part of the group `Group Policy Creator Owners` with the description: `Members in this group can modify group policy for the domain`
so by having the permission to modify group policies, and the permission to `WriteGPLink` to the entire domain, theoretically this user should be capable of escalation his permissions to domain admin.

First ill create a new GPO
![[Pasted image 20251025163233.png]]
and then ill link the new GPO to the domain
![[Pasted image 20251025163259.png]]
now ill open a revshell listener
![[Pasted image 20251025163323.png]]
with SharpGPOAbuse.exe, ill add a computer task that will initiate a revshell, this task will run as NT Authority as soon as i run `gpupdate` and the new task will take effect

![[Pasted image 20251025163507.png]]
![[Pasted image 20251025163520.png]]
