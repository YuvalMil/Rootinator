starting enum, host is running many services, some http services, smb, 2 different ssh ports, and more
i start with quickly checking smb, confirming its not important right now
i run a more accurate nmap scan with `-sC -sV` and it reveals a few things
![[Pasted image 20251028195403.png]]
the http service at 8081 is redirecting to a exhibitor endpoint at port 8080
looking around in that endpoint i figure that this is a web ui for an application called `ZooKeeper`
![[Pasted image 20251028195507.png]]
another interesting thing i notice in nmap is the built date for this version of `ZooKeeper` 
![[Pasted image 20251028195601.png]]
2014 is 11 years ago, surely there are some vulnerabilities
while searching for zookeeper exploits, i stumble upon an [exploit](https://www.exploit-db.com/exploits/48654) for the `Exhibitor Web UI`
![[Pasted image 20251028195719.png]]
![[Pasted image 20251028195752.png]]
![[Pasted image 20251028195802.png]]
got a revshell as charles and fetched local.txt
ran `sudo -l` for checking quickly my next step
![[Pasted image 20251028195854.png]]
reading about gcore in GTFObin
![[Pasted image 20251028195914.png]]
taking a look at the running processes, probably only care for processes owned by root
![[Pasted image 20251028200036.png]]

now i run `gcore` and use `strings` on the file 

![[Pasted image 20251028200228.png]]
in the dump:
![[Pasted image 20251028200303.png]]
![[Pasted image 20251028200328.png]]
