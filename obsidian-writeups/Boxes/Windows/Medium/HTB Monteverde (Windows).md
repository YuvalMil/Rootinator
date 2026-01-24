starting enumeration with nmap, its a classic DC.
smb enum gives nothing, ldap enum with null auth retruns a lot of info. i generate a user list 
`ldapsearch -x -H ldap://10.129.228.111 -D '' -w '' -b "DC=megabank,DC=local" | grep "CN=Person" -B34 | grep "sAMAccountName" | awk '{print $2}'`
i run impacket-getNPUsers with the list, found nothing. i run my NXC wrapper with the list, found a set of creds

![[Pasted image 20251018201440.png]]
i check smb access of the user `SABatchJobs` and find custom shares
![[Pasted image 20251018201602.png]]
in `users$` i find an interesting file
![[Pasted image 20251018201638.png]]

it seems theres a clear text password here, so i spray it over the usernames list
![[Pasted image 20251018201723.png]]
found another set of creds
`mhope:4n0therD4y@n0th3r$`
i already grabbed bloodhound files with the previous user creds, so i know that mhope has WinRM access. i authenticate and grab user.txt
i ran winPEAS and found interesting stuff, a `.azure` directory in my users home folder, also the host is running sql server that is only listening locally.
from this point on, following the walkthrough because i have 0 experience with pentesting azure
first we notice ADSync is installed due to the presence of it in program files
![[Pasted image 20251018202155.png]]
we can get more info using the registry 
`Get-Item -Path HKLM:\SYSTEM\CurrentControlSet\Services\ADSync`
![[Pasted image 20251018202236.png]]

now we know the name of the binary, so its possible to enumerate further and get more details 
`Get-ItemProperty -Path "C:\Program Files\Microsoft Azure AD Sync\Bin\miiserver.exe" | Format-list -Property * -Force`
![[Pasted image 20251018202448.png]]
the walkthrough shows what kind of research had to be done 
![[Pasted image 20251018202605.png]]
[the blog post](https://blog.xpnsec.com/azuread-connect-for-redteam/) 

now i follow the the walkthrough and edit the script to fit my target environment, fixing the SQL connect data, and the different variables. lastly, rerun WinRM with the -s flag to run scripts from a directory in memory, and execute the new script to get creds
![[Pasted image 20251018203518.png]]
![[Pasted image 20251018203655.png]]