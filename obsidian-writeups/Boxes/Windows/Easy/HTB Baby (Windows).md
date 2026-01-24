typical windows machine, null auth is allowed
grabbing ldap data reveals all users and the description for `Teresa Bell` shows initial password
make a user list and spray the password with NXC, found set of creds
`Caroline.Robinson:BabyStart123!`
change password to allow auth
grab bloodhound files, Caroline is `Backup Operator`
WinRM as caroline, grab user.txt and download the backup operator to DA PoC
use PoC to extract hives, download hives to local kali
use Impacket-Secretsdump to parse the hives and get DA NTLM hash
WinRM as Administrator and grab root.txt
