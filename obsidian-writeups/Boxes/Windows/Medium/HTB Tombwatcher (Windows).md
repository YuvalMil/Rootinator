
we start with a set of creds: henry / H3nry_987TGV! 
nmap scan shows a DC with http,smb and winRM the rest are noise
html enum gives nothing, and there is only a IIS startup page, smb doesnt have any custom shares, i grab bloodhound files and look at the permissions of my current user, i manage to find a pretty interesting chain.
![[Pasted image 20251011144858.png]]
basically, from my current user `henry` there is a clear path that takes me all the way to having `GenericAll` on the `ADCS` `OU`, and although it is currently empty, it is definitely better than what i have now
so i begin with abusing `WriteSPN` to obtain `Alfred` password, since i can do a targeted kerberoast, this python tool is perfect for it.
![[Pasted image 20251011105904.png]]
i easily crack the hash using john and find the password to be `basketball`
next i need to abuse `AddSelf` to get `Alfred` into `Infrastructure` 

![[Pasted image 20251011105938.png]]
now i can use a nice tool called `gMSADumper.py` to get the NT hash for `ansible_dev$`

![[Pasted image 20251011105836.png]]
with this hash, i actually didnt manage to use pth-net or bloodyAD, but i saw an example of someone using the hash from `gMSADumper.py` on impacket script, so i used `impacket-changepasswd` to reset `sam` password

![[Pasted image 20251011112053.png]]
now that i also have access to `sam` i can set myself as owner of `john` 

![[Pasted image 20251011112417.png]]
now that i am the owner, i can give myself full control over the object `john`

![[Pasted image 20251011112604.png]]
and then force change the password

![[Pasted image 20251011145937.png]]
at this stage its not so clear how to proceed, but i started with granting the user `john` `FullControl` on any object in the `OU` `ADCS`, even tho this `OU` is currently empty, it might not be in the future.
![[Pasted image 20251011151525.png]]
i tried using this impacket script but it didnt really work for me, and im not sure why, i decided to follow the guide on [SpecterOps Website](https://bloodhound.specterops.io/resources/edges/generic-all) 
it shows how to reach the same result by using PowerView
![[Pasted image 20251011151658.png]]
after doing that, i started looking around, i also tried `certipy find` as john, but it found no vulnerabilities.
although when i looked at the unfiltered output, i noticed something strange, one of the users was displayed as his SID
![[Pasted image 20251011151920.png]]
i tried to quickly run a `--rid-brute` using NXC, but the result was suspicious, the last user was 1108, i didnt have any user at 1111
![[Pasted image 20251011152019.png]]
at this point i suspected we might have some deleted users in the domain, and even though i dont have an indication that my user has the right permission to revive users, i decided to try

![[Pasted image 20251011152205.png]]
`Get-ADObject -Filter 'isDeleted -eq $true -and ObjectClass -eq "user"' -IncludeDeletedObjects | ft Name, DistinguishedName`
with a command i found online i checked for deleted accounts, and found 3 deleted accounts with the same name
it took me a while to figure out how to restore all of them, but eventually i found that i have to change their names after revival in order to keep reviving all 3
what i found to be working is running 2 seperate PS commands for each revived user:
`Set-ADuser REVIVEDUSER -PassThru | Rename-ADObject -NewName NEWNAME`
`set-aduser -identity REVIVEDUSER -displayname NEWNAME -givenname NEWNAME -samaccountname NEWNAME`
and this part was abit repetitive but it only took me a while because i didnt know how it worked, but it was a simple chain of:
Revive account with `bloodyAD` > Reset account password using `net rpc` > Run 2 PS commands to change all name variables > Repeat

![[Pasted image 20251011152932.png]]
`python3 bloodyAD.py -u john -d tombwatcher.htb -p 'asdasd123' --host 10.129.138.227 set restore 'cert_admin'`
`net rpc password "cert_admin" "asdasd123" -U "tombwatcher.htb"/"john"%"asdasd123" -S "10.129.138.227"`
and now, running `certipy find` as the 1111 user, i actually get a vulnerable template

![[Pasted image 20251011153241.png]]
![[Pasted image 20251011153310.png]]
to exploit this i just followed the steps from [certipy repo](https://github.com/ly4k/Certipy/wiki/06-%E2%80%90-Privilege-Escalation) 
but basically this was a specific exploit for **CVE-2024-49019**. 
the certipy wiki described 2 ways to attack, the first one didnt seem to work for me, so i went for the second one, which also didnt work at the beginning, but it was due to my mistake of using full FQDN 

![[Pasted image 20251011153954.png]]
i start by requesting a certificate of the vulnerable template while including the 'Certificate Request Agent' application policy
`The attacker's cert_admin.pfx now contains a certificate which, if the CA is unpatched, includes the "Certificate Request Agent" Application Policy, allowing it to act as an enrollment agent certificate.`

and then i attempt to use the agent cert i just generated, in order to request for a certificate on behalf of a privileged user

![[Pasted image 20251011153919.png]]
and finally i can obtain the NTLM hash by using `certipy auth` 
![[Pasted image 20251011154402.png]]
