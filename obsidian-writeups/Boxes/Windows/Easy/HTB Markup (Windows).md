starting with nmap theres 22,80,443 
i start with http, there is a login page in the site
i try some basic combinations, admin:password logs me in
theres an order section in the website that works with a post request in XML format
![[Pasted image 20250919064618.png]]
in the page source of the order page theres a comment made by "Daniel"
if we try a simple XXE payload, we can easily generate an error that shows this is a windows host, but this could be gathered from the nmap scan too
![[Pasted image 20250919065230.png]]
we check if file inclusion is possible using XXE by trying a basic windows file like win.ini
![[Pasted image 20250919065307.png]]
now since we have this file inclusion vuln confirmed, and we suspect the host might have a user named "daniel" we can attempt to read his private ssh key by guessing the default path
![[Pasted image 20250919065449.png]]
now i can login to daniel using ssh, and i get user.txt
looking at the C:\ directory on the host, theres a unusual file called Log-Management, and inside a bat file "job.bat"
![[Pasted image 20250919065856.png]]
looking at the bat file, it looks like it can be run by admin only, and it runs the wevtutil command with -el first which is enum logs (list log names) and then the -cl flags to clear the logs
checking the running process on the host, we get a hint that this script might be running already
![[Pasted image 20250919070208.png]]
since we cant execute the file, maybe we can modify it, so i check permissions set on the file with icacls 
![[Pasted image 20250919070542.png]]
and it seems that all builtin\users has full control over this batch file, this means all i have to do is use certutil to download nc.exe from my kali to this host, and just change the contents of the bat file to a simple nc revshell
![[Pasted image 20250919071042.png]]
this results in administrator revshell in my listener next time the bat file is executed.