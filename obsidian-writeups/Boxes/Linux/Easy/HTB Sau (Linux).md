nmap shows 80,8338,55555
on 80, i find an application that is vulnerable to SSRF, and once exploited, lets an attacker have an internal proxy to the host.
with that SSRF vuln, i create a proxy to maltrail login page, which is vulnerable to RCE in this version (runs on port 80 )
downloaded and executed a python exploit for this RCE which gave me revshell
as the user puma we have access to user.txt, and also passwordless sudo -l
sudo -l shows its possible to run sudo systemctl status trail.service, with no password
i wasted a lot of time here, didnt know the exploit to this - after using the systemctl service command, a pager will be opened, by default using /bin/less
while in the pager, typing !sh instantly put me in a root shell
it seems that the sudo systemctl command opened a session for root, and the session wouldnt close until the /bin/less pager is terminated - executing commands from insider the pager will be as root
grabbed both flags
