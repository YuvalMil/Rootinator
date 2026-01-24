after nmap scan, smb is the best vector, as there is no http
using nmap script scan, scanned LDAP for host name and domain name properties
updated etc/hosts and etc/krb5.conf files to match the hostname and domain name
used impacket to generate TGT with the given credentials, exported TGT to KRB5CCNAME (from this point on had to use faketime for most of the commands)
used the generated TGT to list SMB shares with netexec, smbclient and crackmapexec wouldnt work with this host SMB version
authenticated using impacket smbclient, found on share IT a password protected .xlsx file
used office2john, cracked the file password, inside found svc_ldap and svc_iis accounts credentials
still using the tgt from the given credentials, ran bloodhound remotely to check for escalation vectors

| faketime "$(ntpdate -q 10.10.11.76 \| cut -d ' ' -f 1,2)" bloodhound-python -u ryan.naylor -p 'HollowOct31Nyt' -c all -d voleur.htb -ns 10.10.11.76 --zip --dns-timeout 30 -k |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

bloodhound showed svc_ldap has SPNWrite permissions over svc_winrm, which allows to create a new SPN for the winrm account
using impacket-GetUserSPNs we can request a TGS for the new fake SPN, the impacket script will automatically convert the retrieved TGS to crackable string for john / hashcat
using john to crack the service account password (because the TGS is encrypted with the service account user password)
with the svc_winrm account compromised, create a new TGT and export it to KRB5CCNAME
use the TGT to connect to the target with evil winrm
retrieve flag user.txt
