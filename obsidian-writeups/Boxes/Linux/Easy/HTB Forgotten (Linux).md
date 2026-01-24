nmap shows only 80 and 22,
http index file is forbidden 403, i start with dir bruteforcing using dirsearch
at /survey i find a uncomplete installation for LimeSurvey
i complete the installation using my kali host as DB host, since i dont know creds for target DB
from here i can login as admin and get RCE by uploading custom plugin, found PoC on github after some google searching
after getting revshell, it seems i am limesvc user in a docker container
checking the env, the limesvc user password is listed there
![[Pasted image 20250925223353.png]]
the password also works for SSH, so i get foothold on the host
also, with the password i get root inside the container, as this user can run unlimited sudo
for privesc:
looking at mount output inside the container, i see a whole dir was mounted from the host
![[Pasted image 20250925223526.png]]
i can attempt to upload / create  a custom file to the dir and look for it on the main host, it might retain any changes set to it from inside the container
after testing it seems i can find files on the main host at /opt/limesurvey, using my root permissions inside the container, i copy /bin/bash into the dir, and set SUID on it with chmod u+s ./bash
now with my SSH connection, navigate to /opt/limesurvey, and run ./bash -p
this lands me in a privileged shell, and i can read root.txt
