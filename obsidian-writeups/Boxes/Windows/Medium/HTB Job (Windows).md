starting enum with nmap, theres rdp, smb, http, winRM and smtp - quickly check SMB for guest or null auth, theres nothing so move to http enum
dirsearch doesnt find anything, the main page has a big clue
![[Pasted image 20251015195430.png]]

first i thought it will be a NTLM leak, so i looked around and found a [github repo](https://github.com/rmdavy/badodf/tree/master) exactly for that, a python script that generates a odt file which leaks ntlm when opened. the script worked well, and i managed to get the NTLM leak, but couldnt crack it / relay it. so i moved on to search for RCE exploits / CVE's or whatever i can find, and found nothing that works.
whatever i tried failed. so i decided to try a dumber approach, and just craft a malicious macro using another [repo](https://github.com/0bfxgh0st/MMG-LO), this script also worked very well, it automatically creates a revshell payload with the given arguments
![[Pasted image 20251015195934.png]]
i then used another very useful [tool](https://gist.github.com/boina-n/e43b996fa0f520c918e3ed6beb754447) i found, a shell script to send emails with attachments using telnet, all i had to do is change the parameters like recipient, file location, server address and execute the script
![[Pasted image 20251015200131.png]]

it worked, the target had automatic script execution with no protection, now with foothold i begin enumeration and the first thing i checked is the web server files - i checked and saw theres nothing to uncover, so i tried to put a file in wwwroot as a test, and it worked.
now i knew there is chance that a webshell could help me elevate perms to a system user, but i didnt bother checking, i thought id like a webshell on the target anyways for convenience and persistence, i quickly uploaded an aspx webshell, and tested whoami
and fortunately, it was indeed a system user.
![[Pasted image 20251015200449.png]]
i elevated to a revshell using nc.exe that i uploaded to the target, and i also checked the system user privileges through the webshell, and confirmed an easy route to root

![[Pasted image 20251015200625.png]]

SeImpersonatePrivilege is enabled, and thats very easy to abuse, and i could do so with many different tools
this time i used both [GodPotato](https://github.com/BeichenDream/GodPotato) and [PrintSpoofer](https://github.com/dievus/printspoofer) 
GodPotato worked well, and i managed to grab root.txt using it, but i couldnt make it pop a revshell using my nc.exe, i tried many different syntaxes, and also tried some different methods like creating a ps1 file with a revshell payload, but idk why it didnt work.
PrintSpoofer made my life a lot easier in this box, all i had to do is run it with the argument `-c cmd` and it popped a root shell, even tho for the first time it failed (i think cause my shell started to lag)

![[Pasted image 20251015201056.png]]
