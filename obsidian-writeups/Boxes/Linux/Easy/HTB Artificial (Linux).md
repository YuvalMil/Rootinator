retarded ctf so short summary
port 80 open, mini webapp to upload and execute ai models, the site provides a dockerfile with the vulnerable dependencies to abuse CVE-2024-3660
found in the app files the db, which contains md5 hashed passwords, i crack the md5 and get password for gael user
gael user is in sysadm group, checking for files with that group, i find backrest backup archive, i try to run backrest, it cant run cause its already running at port 9898
inside the backup archive i find bcrypt hash for the backrest user, i use ssh portforwarding to get to the web GUI from my host, create a backup in /tmp for the /root directory, and use the backrest web command panel to get the contents of id_rsa
`dump SNAPSHOTID "FULL FILE PATH"`
then i can just ssh as root.
