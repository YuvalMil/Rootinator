start with nmap, target looks like DC - kerberos ldap, sldap DNS... RDP also open
SMB is open and no HTTP, so starting with SMB enum
1 share is readable as guest
in the share, a DB file for microsoft access
open the file on my windows host, it contains a VBA macro, look at the macro, find hardcoded credentials for the ldapreader user
get bloodhound-ce-python files with ldapreader user
notice many machine accounts, 1 is pre 2k access (password is same as username but only in lowercase)
machine accounts can change each other password, also 1 machine account is able to add itself and other users to 'services' group
services group is also able to login with RDP
verify login creds to FS01$ host with netexec smb
STATUS_NOLOGON_WORKSTATION_TRUST_ACCOUNT error means creds are valid
change password to the account with impacket-changepasswd
then change password to the ADMWS01$ account 
use ADMWS01$ credentials to add ldapreader to 'services' group
use ldapreader to login with RDP, get user.txt flag
notice the OS is old, the GUI looks very outdated, check for specific version privilege escalation exploits

Microsoft Windows Server 2008 R2 Datacenter
6.1.7601 Service Pack 1 Build 7601

found an article that shows privilege escalation in this version using Perfusion
download the tool on windows, build with visual studio
copy built application to kali, reopen RDP with shared folder containing the build
run Perfusion with Perfusion.exe -c cmd -i
got system privileges, grab root.txt from C:\users\administrator\desktop
