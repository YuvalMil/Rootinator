


at the very start, was stuck at an upload function, wasted time trying to reverse engineer the upload mechanism, noticed the SSRF at the "upload from URL" function, but didnt attempt to escalate it, it was wrong.
had to use the SSRF to scan all ports on the host for http services that run locally, it wouldve lead me to port 5000, which leaks a password for SSH user dev. (i used burp pro, in the exam i need to use ffuf)

Couldnt figure out how to priv esc, had sudo rights to a python script
![[Pasted image 20250916211519.png]]
wasted a lot of time on trying to pop a pager by using git, which was wrong.

had to try to google "python from git import repo vulnerability", it wouldve lead me to CVE-2022-24439

![[Pasted image 20250916211809.png]]