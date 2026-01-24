first nmap scan doesnt show any results, i try FIN scan on common 1k ports, also nothing.
i try UDP scan, port 500 is open, i read some online to see this port is usually IPSEC / IKE VPN service
as i never tested this service before, i read some online guides, most helpful was: https://angelica.gitbook.io/hacktricks/network-services-pentesting/ipsec-ike-vpn-pentesting
using the guidance, i manage to find an ID "ike@expressway.htb" and i also retrieve a hash, which i manage to convert and crack with john
![[Pasted image 20250926182437.png]]
![[Pasted image 20250926182500.png]]
![[Pasted image 20250926182516.png]]
i try to SSH with the creds i found, and it works, so i get a foothold and user.txt
since i have the password, i check for sudo privileges, and there are none. i check SUID binaries, and find an unusual binary exim4, but after checking the version and looking for vulnerabilities online, i find that this exim4 version is patched and is using 
a rather new build, released on august, 1 month ago. (seeing this discourages me from continuing this path, and i decide to check something else that bothered me )
looking at the SUID binaries again, theres something else odd, i can see 2 different sudo binaries.
![[Pasted image 20250926182843.png]]
checking both binaries version, i find that one of them might be vulnerable to a priv esc vulnerability
![[Pasted image 20250926182941.png]]
the vulnerability is  CVE-2025-32463 and should affect this version of sudo, and theres also a quick way to check if vulnerable
![[Pasted image 20250926183059.png]]
![[Pasted image 20250926183201.png]]
i copy the script from the repo, create a new file on /tmp, add execution privileges, and run - i instantly get a root shell, and can grab root.txt
