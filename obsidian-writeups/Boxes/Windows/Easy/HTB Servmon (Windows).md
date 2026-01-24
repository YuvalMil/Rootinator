few interesting services, smb, ftp, http and few more
smb guest doesnt work -- ftp anonymous works, i find a file that talks about the location of passwords file on the user Nathan desktop, so i know the location is C:\Users\Nathan\Desktop\Passwords.txt
i check http, its running an app called nvms-1000, i check for exploits and find out its vulnerable to path traversal which is perfect for me, so i can read passwords.txt 
![[Pasted image 20250930220513.png]]
the files in the ftp server revealed 2 usernames, Nathan and Nadine, theres also SSH running on the server, so i can attempt these passwords with both users
i find out the SSH password for nadine is L1k3B1gBut7s@W0rk
i login with ssh and get user.txt
at 8443 i have NSClient++ running, i know i need to find the password to it, after checking online i find out the password is in cleartext in the app folder
![[Pasted image 20250930220757.png]]
the access to the NSClient is blocked from outside, i use chisel to create a socks proxy
there is a way to PE with NSClient since its running as nt authority, found this script on [github](https://github.com/xtizi/NSClient-0.5.2.35---Privilege-Escalation/tree/master)
generated a exe payload with msfvenom, uploaded to host with certutil, and ran the exploit with proxychains
![[Pasted image 20250930221044.png]]
box rooted, i will add the walkthrough even tho its somewhat retarded