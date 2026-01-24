nmap shows 80 and 22, i start enum on http, i get redirected to domain usage.htb so i add in hosts file
there is a vulnerable reset password function on the webpage, a single quote mark will result in error 500, after long enumeration with sqlmap, i find the hash to the admin panel, which cracks easily with john
i get admin panel creds admin:whatever1
i identify the admin panel and version, it is a vulnerable version of laravel-admin. i found an exploit on github which works perfectly [CVE-2023-24249](https://github.com/IDUZZEL/CVE-2023-24249-Exploit)
now i have foothold as the user dash, i check /home and see theres another user called xander, so i assume i should get access to that user
checking the user dash home directory, i find .monitrc file, with credentials for the web admin panel, the password in the monitrc file also works as the password for the user xander
checking xander sudo permissions i see he is allowed to run a custom binary called "usage_management" i attempt to run it, and have 3 options to choose from
![[Pasted image 20250926222539.png]]
i check for -h or --help to find extra usages, but it doesnt work, i also check strings and try to find a clue, but there is none
i run every single option to see the output - the 3rd and 2nd options give almost no output, which convinces me i should probe further the first option.
when choosing the first option, judging by the output, it adds everything under /var/www/html to a zip archive and puts the archive in /var/backups
the output from strings also seem to suggest something of this sort
![[Pasted image 20250926223003.png]]
i decide as a test, to create a directory in /var/www/html, and link /root to the new directory, then i run the binary with sudo, and copy the created zip to /tmp
then i open a python http server on the target, and download the zip file to my host for convenience (could also SCP) 
unzipping the archive, i get access to root.txt and roots SSH key.
then i just login as root to verify the key is working.
![[Pasted image 20250926223527.png]]
