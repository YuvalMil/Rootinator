we start with a pair of creds, and a few interesting ports, mainly 445 and 1433
i find nothing on SMB with my given creds, and i move to MSSQL - i find that xp_dirtree is enabled and accessable with my user - so i attempt to crack the NTLMv2 for DC01$, even tho its unlikely, i also find that there is a linked MSSQL server,
and my linked access is actually DB admin, so i can enable xp_cmdshell and get code execution on the VM.
at this point what i need is to escalate privileges on the VM, after running winPEAS, i shouldve noticed that the OS running on the VM is quite old, and its possible to find a local Privesc exploit on metasploit / github (i used metasploit)
with the new meterpreter session that runs as system, i download rubeus.exe to the VM, and run it on monitor mode
![[Pasted image 20251009184257.png]]
now rubeus is listening for incoming tickets, and will capture any ticket used for authentication on the VM
i can easily force an authentication through my MSSQL session
![[Pasted image 20251009184411.png]]
![[Pasted image 20251009184429.png]]
now the process is very simple, copy the entire base64, echo it into a file on my local host

![[Pasted image 20251009184534.png]]
decode it into a kirbi ticket, and convert to ccache using impacket
![[Pasted image 20251009184606.png]]
use the ticket with impacket-secretsdump to perform a DCSYNC, dont forget to fix clock skew
![[Pasted image 20251009184705.png]]
use psexec to login remotely as system
![[Pasted image 20251009184749.png]]
