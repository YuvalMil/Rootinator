start by checking SMB cause its the only interesting port
guest is enabled -- has access to public share -- in public share theres a pdf file with credentials for mssql server
use impacket-mssqlclient to authenticate to the server -- no interesting db's, its all default db's -- no linked servers, no command execution
i can try to make the mssql server to check a file on a remote SMB share, if this works the server will attempt to authenticate to my controlled server with the user running the mssql server and his ntlm hash
`sudo responder -I tun0 -v` -- a controlled server
i had some issues with impacket mssqlclient, so i used sqlcmd, 
![[Pasted image 20250930183740.png]]
got the user and the ntlm hash
![[Pasted image 20250930183759.png]]
i crack the hash using john
![[Pasted image 20250930183856.png]]
i also use these creds to get bloodhound files
![[Pasted image 20250930183930.png]]
sql_svc is in the remote management users group, so i can Evil-WinRM into the target with the compromised creds
i look for interesting files on the host, and i notice a odd directory C:\SQLServer
after looking around, i find C:\SQLServer\Logs\ERRORLOG.BAK
this file wont show passwords in clear text, but it seems that the user entered his password as username, so it is now compromised
![[Pasted image 20250930184303.png]]
i create another Evil-WinRM session as the user Ryan.Cooper, and look around in the Bloodhound files, but i dont notice anything obvious.
i run certipy-ad, and find a vulnerable template
![[Pasted image 20250930184456.png]]
![[Pasted image 20250930184512.png]]
Im not very familiar with these vulns, so i check [online](https://abrictosecurity.com/pentesting-active-directory-certificate-services-adcs-esc1-esc8/)
following the guide, i exploit the template and get the Administrator NTLM hash, theres no need to crack it, since NTLM auth is enabled i can just use Evil-WinRM with passthehash flag
![[Pasted image 20250930184747.png]]
