nmap shows only 80 and 22 - i immediately check http, it redirects to cozyhosting.htb
started subdomain search and dirsearch, dirsearch found interesting endpoints at /actuator
/actuator shows other endpoints, one of them is /actuator/sessions, which leaks current users session tokens
i take the only valid session in the list, use it as my cookie, and send a GET request to /admin - there i find a single page with a ssh connect function, that askes for hostname and username
![[Pasted image 20250916163801.png]]
this is hinting at command injection, i capture the request in burp, and try the username ;a
![[Pasted image 20250916163915.png]]
verified that it is indeed a command injection point, i try a shell immediately, but spaces are not allowed 
![[Pasted image 20250916164015.png]]
the bypass:
![[Pasted image 20250916164059.png]]
the revshell payload is echoed into base64 -d and then passed into bash, i avoid using spaces with the {} (just the way bash interprets this syntax)
![[Pasted image 20250916164323.png]]
this lands me a revshell as the app user in the /app directory, and this directory only contains 1 file, a JAR archive
i open a python http server on the target, and download the jar to my host, there i unzip it and start looking for config files
![[Pasted image 20250916164546.png]]
i found a file with the DB user credentials, i try a few commands on the host to connect to the db, but it doesnt seem to work, so i decide to search manually for a postgresql binary in /usr/bin - i find the binary psql
had some trouble connecting with psql, eventually it worked after specifying localhost in the command - `psql -U postgres -W -d cozyhosting -h localhost`
i use google to get some help with command syntax, i get 2 bcrypt hashes from the DB
![[Pasted image 20250916164820.png]]
i copy both into a file on my kali, and run john with rockyou
john finds the password to admin - manchesterunited
i try this password for the user that has a directory in /home - josh, and it works
i login as john using ssh, try sudo -l, it seems john can run ssh with sudo
went on GTFObins, ssh with sudo is instant root access with the command:
![[Pasted image 20250916165032.png]]
grabbed both flags as root
