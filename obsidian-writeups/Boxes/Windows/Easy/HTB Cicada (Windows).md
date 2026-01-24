start with nmap, note SMB, ldap, kerberos
attempt guest authentication for SMB shares enum - guest can read HR share
in HR, a txt file - contains the default password for new users
brute force RID with guest user to find more users
attempt to auth with default password and different users using nxc
find compromised user 'michael.wrightson'
use bloodhound-ce-python to get domain information
user DAVID.ORELIOUS has his password in AD user description
access DEV smb share using DAVID.ORELIOUS creds
find powershell backup script that contains creds for 'emily.oscars'
start Evil-WinRM with using emily.oscars creds
get user.txt
emily.oscars is part of the backup operators - can read all system files
copy SAM and SYSTEM hives to c:\temp
download to kali using Evil-WinRM
use impacket-secretsdump to extract administrator NT hash
use Evil-WinRM pass the hash for a privileged session
get root.txt 