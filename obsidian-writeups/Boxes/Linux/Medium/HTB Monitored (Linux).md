nmap TCP scan shows http, https, ldap, and unknown service at 5667
checking HTTP leads to HTTPs /nagiosxi which is running an unknown version of Nagios XI
Dirsearch on both http and https lead to nothing
default credentials didnt work, ldap enum didnt give anything usefull, subdomain scan didnt reveal anything
had to perform a UDP scan, which reveals snmp at 161
by enumerating snmp with nmap / snmpwalk its possible to get the password to the user svc
when trying to authenticate as the user on the web GUI, it shows a unique error, account doesnt exist or disabled
this error only shows with the correct password for the user
according to the walkthrough, when looking for a way to resolve this issue, we should stumble upon a forum thread that shows how to get a authentication token straight from the API
using the API request in the forum thread, we are able to get a valid token, and sign in by using it in the ?token parameter
once authenticated, the version of nagios XI is showing at the bottom left
searching for vulns in this version, should come upon an authenticated SQli vuln
found a detailed PoC of the exploit, and used it to generate a valid POST request in burp, that i can save and use in SQLmap to dump the DB
using the admin API key from the DB, im able to create a new admin user with any password i want
after creating and logging in as the new admin, im able to create a new "check command" in the nagios XI system, which will contain a revshell bash command and run it on the host, which gives me a foothold
after grabbing the first flag, i type sudo -l, and find out that the user has access to many commands as root
many of the allowed commands are scripts, by reading each script, i find 1 that might be vulnerable to symlink attack
![[Pasted image 20250914182414.png]]
![[Pasted image 20250914182455.png]]
only thing left to do is create a key file, change permissions, and login as root to get the root.txt flag