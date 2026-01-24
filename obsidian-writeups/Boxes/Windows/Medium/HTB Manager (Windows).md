smb http and mssql, http and mssql give nothing, smb has guest enabled, im able to rid bruteforce and get a list of users
by attempting to login with lowercase username as password, i find the creds Operator:operator
signing to mssql with the creds, i find out that xp_dirtree is enabled and working
![[Pasted image 20251007193451.png]]
![[Pasted image 20251007193506.png]]
by looking at the wwwroot dir i find a backup file on the web server
![[Pasted image 20251007193551.png]]
downloading it through the browser and opening it reveals a config xml file, it contains the credentials for the user 'raven'
as raven i get WinRM access and find first flag
by running certipy find as raven, i find that his CA permissions are misconfigured and lead to ESC7 vulnerability
![[Pasted image 20251007193758.png]]
there is a very detailed guide on how to exploit ESC vulnerabilities [here](https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation)
by following the guide, i end up with administrator's NT hash, and im able to login using Evil-WinRM and PtH techniques
![[Pasted image 20251007193946.png]]
![[Pasted image 20251007194001.png]]
