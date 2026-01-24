started with nmap, saw 80 and 22 and started checking http
found nothing with enumeration, no dirs, no vhosts, no interesting functions
checked UDP ports, found snmp is running, so ran all nmap snmp scripts, found credentials
![[Pasted image 20250918191106.png]]
logged in with creds to SSH, started enumerating
i know that the info i need is in the PandoraFMS directory
the DB config file is not readable for my user, and i dont see the web service for pandoraFMS running on a different port, so i check the /etc/apache2 dir
in the sites-available dir, there is a pandora.conf file, in there i find that the pandoraFMS is running on 80 as well, but on localhost only
![[Pasted image 20250918191508.png]]
i use the ssh creds to do a port forwarding, and make the pandoraFMS available on my localhost:8089 
there i meet the login page - immediately search on google for default passwords - but none of them work.
then i try to login with my known creds, but it says the user can only use the API, so i waste more time reading about the API usage and limits, and i actually find a way to get password MD5 hashes of the PandoraFMS users
but this is also the wrong way to go about it, none of the hashes are found in rockyou.txt or other wordlists, and i cant find a way to authenticate with the hashes alone.
eventually i look for vulnerabilities using the version, and i find a SQLi login bypass poc on github, which lets me login as admin (the walkthrough didnt use this method, they used another SQLi vulnerability which lets you steal the session of the user "matt")
since i actually authenticated as admin, i can just upload a webshell using the application GUI, and then search for it on my SSH session to find out the path, then i just transfer to a revshell and start looking for priv esc
typing sudo -l shows me im in a restricted shell
![[Pasted image 20250918192258.png]]
since we have /usr/bin/at escaping from the restriction is possible
![[Pasted image 20250918192352.png]]
![[Pasted image 20250918192409.png]]
when looking at SUID binaries, 1 stands out, not only it is a custom binary that isnt default to the system, i also have permissions to execute it
![[Pasted image 20250918192510.png]]
using ltrace, i can see that the binary is trying to use tar without specifying the full PATH, so i simply create a fake tar - echo "/bin/bash" > /tmp/tar 
change the PATH to include tmp, add execution permission to the fake tar, and run the binary again.
this lands me a root shell, and i can grab both flags.
