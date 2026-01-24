start with nmap, only 80 and 22 are open
check http, its a grafana server, running version v8.0.0
instantly check for vulns, seems like there are many low-med severity, no unauth RCE, but there is a path traversal and file system access at a certain endpoint
find the endpoint and confirm the vuln, checked by seeing passwd
start checking online about pwning grafana, maybe the location of secrets in config files or similar
took some time, but managed to find all necessary files to move on + a tool that came in handy 
files needed: 
/var/lib/grafana/grafana.db
/etc/grafana/grafana.ini
explanation:
the grafana DB holds the users password hashes + salt, as well as datasources password hashes, both could be used in my case, but in this CTF the data_source table in the db is empty, so i only have users hashes
2 users in the DB, boris and administrator
i found a tool that converts grafana hashes to hashcat format for cracking, which also shows and demonstrates the whole tool workflow, which worked very well
https://github.com/iamaldi/grafana2hashcat
converted both hashes + salt to crackable format and started hashcat, immediately the password for boris was found to be: beautiful1
i let hashcat keep going since i might need to crack both passwords, in the meantime i login to boris on grafana to check for the user capabilities after auth
the web UI seems very dull and without any interesting functions, so i attempt ssh to the server, i wasnt able to see boris in the passwd file using the grafana vuln, but i assumed it couldve been a container passwd, and it turned out to be right -
as i logged in to boris using ssh and the compromised password
sudo -l shows i can run docker exec *
after a bit of investigating, i decided to use an AI for more direction, and tried the following:
check mount to see where main host file system is located, notice its at /dev/sda1 which is pretty basic
since i can run sudo docker exec with any flag, i can also run the --privileged flag, which will run any command i input with full system capabilities, basically ignoring most security mechanisms
i attempt mount /dev/sda1 /mnt and it seems to work
i run sudo docker exec -t -i --privileged -w /mnt/root -u root grafana ls
and found root.txt
FOR TRAINING - 
wanted to escalate the file system access to full root SSH access
looked at sudoers file, saw group admin and sudo can run all sudo commands, once i have that i can simply log into root with su root
copied the whole /etc/group to my kali host
added my user boris to the sudo group
wget inside the container to /tmp, and then replace the group file with mv group /mnt/etc/group
relog to boris user with ssh
su root + boris password = logged in as root
