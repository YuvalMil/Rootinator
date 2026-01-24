with nmap note port 80 and 8080 are open
find vulnerable xwiki version on 8080, vulnerable to unauthenticated RCE
get revshell as xwiki user
find 1 available user called oliver
in xwiki files, find /usr/lib/xwiki/WEB-INF/hibernate.cfg.xml
inside a string that might be a oliver's password `<property name="hibernate.connection.password">theEd1t0rTeam99</property>`
try to sign in as oliver with ssh, it works
get user.txt
look at SUID binaries, there is a vulnerable netdata plugin, check oliver groups - notice he has netdata permissions
read  CVE-2024–32019 to understand how to exploit it
write small exploit in c on kali, compile and wget to the target, add execution permissions and run
results in root access, grab root.txt
