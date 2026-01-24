nmap scan shows only RDP HTTP and SSH, a bit odd for windows host
starting to enum HTTP, there is not much to discover, no virtual hosts or secret dirs, and only 1 page with a upload function, it says it receives video files compatible with windows media player, but its possible to upload anything without getting an error in return
i suspected that maybe its possible to get a NTLM hash leak through windows media player files, but i couldnt find enough info online, only after checking the walkthrough i found that its indeed NTLM hash leak
eventually i found a very nice page with different hash leak techniques

![[Pasted image 20251012171325.png]]

i create the file just like it shows, and replaced the placeholder with my IP
![[Pasted image 20251012171641.png]]

after uploading i get a auth attempt on my responder

![[Pasted image 20251012171712.png]]

now i attempt to crack it with john
![[Pasted image 20251012171924.png]]
now with a set of creds, i can SSH to get a foothold, and user.txt
by reading the source code for index.php i find the upload function and with it the upload directory which is hardcoded

![[Pasted image 20251012172127.png]]

using powershell `get-acl *` inside that directory, i can confirm that the user creating these files is `NT AUTHORITY\LOCAL SERVICE` 

![[Pasted image 20251012173443.png]]

that means a php webshell would allow me to elevate privileges to a system user. Theoretically if i could create directories inside `Uploads` i could create a junction directory that leads to `C:\Xampp\htdocs` which is the webserver root directory.
checking permissions inside the uploads dir, i find out that everyone has full access
![[Pasted image 20251012173949.png]]
using `php -a` on my kali, i can open an interactive php shell, and copy the folder name mechanism, and that way predict the folder name that will be created by the system user

![[Pasted image 20251012174147.png]]

now back on the target, the mklink command is used to created a junction directory `mklink /j "C:\Windows\Tasks\Uploads\1d883a42c557c9bcb227110b0f58597a" "C:\xampp\htdocs"` 
![[Pasted image 20251012174306.png]]
now uploading a webshell will upload it to the webserver root directory as well
ill use the webshell + nc.exe that i uploaded to the host to get a revshell as the system user

![[Pasted image 20251012174438.png]]

looking at the system user privileges, there is 1 that can be used for for privesc

![[Pasted image 20251012174534.png]]
there is a [github repo](https://github.com/b4lisong/SeTcbPrivilege-Abuse) that showcases a tool to abuse this privilege and gain a revshell as `NT AUTHORITY\SYSTEM`
all i have to do is clone the repo, upload the exe, and run it with a exe revshell i generated with msfvenom

![[Pasted image 20251012174832.png]]
the usage also requires a random string, no idea why

![[Pasted image 20251012174904.png]]
