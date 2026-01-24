starting with nmap theres only 22 and 80
i immediately go to http, theres no domain redirect, i start dirsearching and looking around the site, notice a login button
trying some default creds combinations, doesnt seem to work, no extra dirs as well. i notice there is a /upload endpoint but it requires authentication, so i assume this is a sqli login bypass or something of the sort.
started burp intruder with sqli wordlist on the password param, it worked.
the room is called magic, and a lot of values are translated to hex around the site, i assume this is a hint, i try to upload a few webshells and its all blocked, i decide to grab a obfuscated php webshell from github, 1 that has a spoofed magic byte, the upload works. i check the page source from the landing page to see where the images are sourced from, its from assets/uploads,
i find my webshell, and get a revshell using base64 payload
as www-data i find the php config files, and it contains the DB username and password. i use mysqldump with the credentials and get the password to the user "theseus"
i check groups and notice this user is in a group "users" and there is also a non default SUID binary called sysinfo, which can be executed as root OR by the group "users" so this is a big sign.
using strings on the binary, i find out that the binary is calling other system binaries, but without using the full path
![[Pasted image 20250917182858.png]]
and since this binary has SUID set, and is owned by root, that means when ran by my user, it will use my user env variables, while using root's permissions.
in other words if i can modify the path, i can create a file that will run instead of lshw / fdisk / cat / and make it pop a root shell for me

![[Pasted image 20250917183109.png]]

i create a "lshw" file in /tmp, edit PATH, edit file perms, and run the sysinfo SUID binary, which gives me a root shell.
