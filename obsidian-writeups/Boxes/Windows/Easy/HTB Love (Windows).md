start with nmap, find many ports, notably: 443, 80, 445, 5000,
after service scan find that many more are http
at port 80 find web application "voting system 1.0"
can find authenticated RCE exploits, need to find creds
enumerate web dirs, find admin/login.php
fuzz few creds, find that admin is valid username
look at the certificate at 443, find subdomain staging.love.htb
check staging.love.htb in http, find file scan system
system vulnerable to LFI / RFI 
asking for http://localhost:5000 reveals admin password
download python exploit, change paths and creds so it fits our application
run listener and exploit, get revshell as user phoebe, grab user.txt
start manual enum for priv esc
find AlwaysInstallElevated is true
generate msi payload in msfvenom
transfar with wget / certutil
run with msiexec
get system priv, grab root.txt