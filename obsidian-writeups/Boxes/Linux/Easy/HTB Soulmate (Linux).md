nmap shows 80, 22 and 4369 which is erlang port mapper daemon
i start checking http, i arrive at a online dating web app called soulmate.htb, there is a register, login, and update profile functions, i waste too much time messing with these functions instead of starting from complete enumeration.
after getting to the conclusion that i wont be able to exploit any of these functions effectively without seeing the source code, i start checking for other dirs and subdomains more extensively, and i find the subdomain ftp.soulmate.htb
on that subdomain, i have a running vulnerable version of CrushFTP, and even though i dont have the exact version right now, i decide to test some PoC's
i find an article that talks about CrushFTP auth bypass from Huntress, and it explains how it all boils down to a HTTP request, so i copy the request and send it using burp
![[Pasted image 20250927202356.png]]
[Article](https://www.huntress.com/blog/crushftp-cve-2025-31161-auth-bypass-and-post-exploitation)
the response contains all the user accounts registered in CrushFTP, so i understand this auth bypass is working for my version of CrushFTP, and i start looking for an exploit to automate the procedure
i manage to find a perfect exploit on [github](https://github.com/Immersive-Labs-Sec/CVE-2025-31161) which allows me to create a new admin account
using the admin account, i can check for uploaded files in CrushFTP, and after a bit of searching, i find the directory that contains all the php files from the dating webapp
i waste some time fucking around with settings and configs, because i never used this application in my life, but eventually, i get a working php webshell, and this allows me to get a revshell as www-data on the main host
at this stage i was stuck for a very long time, i was chasing a password that doesnt exist, i thought that to proceed, i must find the password for the user "ben" as its the only user under /home, and i cant do anything with www-data.
i also thought that it makes more sense to first get access to user.txt and only then try to aim for root.txt, although this is usually the case in CTF's, it wasnt in this one.
at some point i decide to stop looking for the password, and start to attempt different stuff, i try all sort of bullshit like trying to upload a symbolic linked file, or trying to upload into a linked dir, and of course it doesnt work
i try looking for interesting erlang files on the system, and when typing `erl -h` i stumble upon the erlang/OTP version on the host which is "Erlang/OTP 27", because i dont know anything about this service / binary, i google "Erlang/OTP 27 pentest"
by some luck, i stumble upon a very interesting [CVE](https://github.com/Immersive-Labs-Sec/CVE-2025-31161) about unauthenticated RCE in erlang SSH, and i already know from previous enumeration, that this host is indeed running Erlang SSH at port 2222

looking at the POC, the author tests on "SSH-2.0-Erlang/5.2.9", and when i try to get a fingerprint from the service running at 2222, i get the same fingerprint
![[Pasted image 20250927204300.png]]
deciding to test it out, i transfer a chisel binary to the target, and connect it to a socks proxy, so i can run exploits on port 2222 from my kali host.
the first exploit i tested didnt work, even when i tried to download it straight to the host and run it locally, but i still felt like this is the right way to proceed so i kept searching
while reading [offsec article](https://www.offsec.com/blog/cve-2025-32433/) about this CVE, i found a link to another PoC on [github](https://github.com/0xPThree/cve-2025-32433) and this one worked perfectly, i tested it first from my host, and tried to curl to my listener, just to confirm execution.
![[Pasted image 20250927204810.png]]
all thats left is to rerun the listener, and rerun the exploit with the revshell payload this time, and the box is rooted.



After looking online to compare solutions, it seems i did miss the step of obtaining ben's password: [Walkthrough](https://infosecwriteups.com/htb-soulmate-walkthrough-ff39e0028c6a) 
i shouldve noticed the running script at /usr/local/lib/erlang_login/start.escript
