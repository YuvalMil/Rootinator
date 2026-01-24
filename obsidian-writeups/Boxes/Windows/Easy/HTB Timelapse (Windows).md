start with nmap - only SMB is interesting
check for guest - enabled and can read /shares
in /shares - a zip file with a password
zip2john - crack the zip, extract the content
pfx cert - pfx2john, crack the secret key
attempted to authenticate with pfx and key, wont work (cant get tgt, cant WinRM)
convert pfx to pem cert and pem key file
use Evil-WinRM with pem cert and key - gain foothold
look around in user files, found PS history file - contains creds for svc_deploy user
validate creds with NXC, checked BloodHound for info about svc_deploy, is a part of LAPS READERS group
use bloodyAD to read laps password on the host - got administrator creds
WinRM as administrator with cleartext password, grab root.txt 
