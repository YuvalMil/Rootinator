starting enum with nmap, target is a typical DC with http server running - ldap smb and rpc checks with null auth give nothing so i stick to HTTP
after long enum process i decide to try vhost enum even tho the server doesnt redirect me to a domain - i actually found a vhost `school` and it has a vulnerable parameter to SSRF, which allows me to leak NTLM
![[Pasted image 20251021170110.png]]
![[Pasted image 20251021170146.png]]
![[Pasted image 20251021170205.png]]
![[Pasted image 20251021170221.png]]
![[Pasted image 20251021170233.png]]
now with a compromised user, i can get bloodhound files and check SMB again for available shares
![[Pasted image 20251021214113.png]]
Shared is an empty share, Web is the root directory for `flight.htb` and `school.flight.htb`, and users is simply `c:\users`
since this is a user that was made specifically to run apache, its possible that his password was reused in the creation of the svc user, so i need to attempt to spray this password over all available users in the domain
![[Pasted image 20251021214327.png]]
the only difference seems to be the write permission in `Shared`, because of the share name, i can assume that other users also have access to this share, and it might be possible to leak another user NTLM hash by adding to the dir files that might execute automatically on access, such as `desktop.ini`
there is a very useful [tool for this purpose](https://github.com/Greenwolf/ntlm_theft), it will generate many different types of files that can leak NTLM

![[Pasted image 20251021205646.png]]
i can upload everything in the dir with `mput *`
![[Pasted image 20251021214830.png]]

NTLM does indeed leak:

![[Pasted image 20251021205703.png]]
and then cracked:
![[Pasted image 20251021214929.png]]
now i grab user.txt with smbclient, and i check for new smb permissions with nxc
![[Pasted image 20251021215042.png]]
this user can write on the web share, thats perfect for RCE, i quickly upload a php webshell, use that webshell to create `c:\temp` and upload `nc.exe` and then i catch a revshell using that nc.exe
with a fully interactive shell, i can finally look around the host, and after enumerating for a bit, its pretty clear that theres a service running at localhost:8000
i check with curl, and there is definitely an HTTP service running at 8000, so i download a chisel to the host, and use it to  set up a socks proxy, now i can check the service at 8000
by checking `c:\inetpub` i can confirm that theres also IIS running on the host, and the web root for the IIS service is at `c:\inetpub\development` 
i tried to upload an aspx webshell using the current shell user (svc_apache because its revshell from the php webshell) but theres no permissions - its possible that c.bum will have permissions, since hes in `Webdevs` group

i download RunasCs.exe to the host, to execute commands as `c.bum` easily, and uploading the webshell works:

![[Pasted image 20251021211722.png]]

![[Pasted image 20251021211736.png]]

now i can get a revshell using the new webshell, which will give me a pretty privileged shell
now comes a very important part of the CTF, the introduction to `Virtual Accounts`: 
![[Pasted image 20251021215959.png]]
so this IIS user doesnt have his own creds, its running using the machine account creds, and this machine account is the DC...
![[Pasted image 20251021220105.png]]
so theoretically i should be able to use rubeus to obtain a TGT for my current user, and using this TGT i should be able to perform a DCsync attack
i download rubeus.exe to the machine and run `tgtdeleg /nowrap` 
![[Pasted image 20251021220313.png]]
![[Pasted image 20251021220329.png]]
according to the output, this is a base64 encoded kirbi ticket, so i need to convert it first - save the b64 ticket to `ticket.b64` decode with `cat ticket.b64 | base64 -d > ticket.kirbi` and then convert using impacket
![[Pasted image 20251021220506.png]]
now i can attempt to use `impacket-secretsdump` with kerberos auth, and i should be able to get Administrator NTLM hash
`impacket-secretsdump g0.flight.htb -k -just-dc-user Administrator -no-pass`
![[Pasted image 20251021220644.png]]
now impacket-psexec for nt authority privileges
![[Pasted image 20251021220744.png]]