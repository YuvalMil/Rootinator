nmap shows only 22 and 80
checking http, its a webapp that uses searchor 2.4.0 according to bottom of the page
check CVE's, found RCE
download and run exploit, got a foothold as user svc, got user.txt
check app config files, find creds for user "cody" at gitea.searcher.htb (a subdomain of the webapp)
password for cody also works for user svc, check sudo -l
sudo -l shows svc can run a python script with root privileges, the script is at /opt/scripts/system-checkup.py
mess around with the script, notice the function docker-inspect
it asks for format argument and container name
the script also allows other commands, like docker-ps, which will show info about running containers
need to read abit about how docker uses --format argument to proceed
after being able to get docker-inspect to work, i can inspect the 2 running containers, 1 for mysql and 1 for gitea
the gitea container inspection includes the password for the user 'gitea', which doesnt exist on the host
i attempt to login to gitea with these credentials, it doesnt work
try the password for the user administrator - works
user administrator has 1 repo which was private, its the repo that we saw at /opt/scripts
check the script system-checkup.py since i have sudo rights to it.
notice the argument full-checkup is supposed to run the script full-checkup.sh, but in the code its written as an open path and not absolute path: 
![[Pasted image 20250908182458.png]]

i try to run the script from /tmp - it fails
i try again from the path /opt/scripts - it runs 
meaning the script is really depending on the current working directory
went to /tmp, created a revshell script, opened a listener on my kali, and ran the python script with sudo
got a root revshell on my listener, grabbed root.txt

