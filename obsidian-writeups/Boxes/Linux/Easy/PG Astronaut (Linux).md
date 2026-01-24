starting enum with nmap, only ssh and http, starting to enum http 
trying the index page of the server, indexing is turned on and i see everything is under `grav-admin`
![[Pasted image 20251029154304.png]]
clicking on it confirms this is an installation of `Grav CMS` 
![[Pasted image 20251029154333.png]]
its an open source project, so i go to the github and try to find default files that might reveal the version of this installation, with no success.
i find the admin login panel and try some basic combinations, with no success
looking around at `Grav CMS` exploits and CVE's, i found an unauthenticated RCE [exploit](https://github.com/CsEnox/CVE-2021-21425), decided to give it a try, and it worked - i now have blind RCE on the server.
tried basic revshell payloads with no success, the exploit takes time to execute each command because its using scheduled tasks, i decide i need to try to get a webshell first.
using a `wget` command in the exploit to grab a php webshell to whatever the current directory is, and hoping its in the root directory - its indeed in the root directory and i now have a webshell
![[Pasted image 20251029154801.png]]
from the webshell simple revshell commands still wont work, ill just upload a php revshell file and use that to get my revshell since it usually works.
it worked and im now `www-data`
![[Pasted image 20251029154920.png]]
i first decide to get the `Grav CMS` users credentials, since i might be able to crack those hashes and use them for lateral movement, i did find the hash for the admin user but wasnt able to crack it, in the meantime i decided to run linpeas and look at the output.
looking at SUID binaries shows a promising vector
![[Pasted image 20251029155217.png]]
a php binary with SUID and owned by root, programming languages binaries often have interactive shells and other capabilities allowing priv esc, so i take a look at GTFObins
![[Pasted image 20251029155330.png]]
the command in that format didnt work, but changing the /bin/sh to the path of bash on the target gave me a privileged shell
![[Pasted image 20251029155524.png]]
i now have a privileged shell but im still www-data, ill add my own ssh key to root's `authorized_keys` and use ssh to login as root
![[Pasted image 20251029155721.png]]
