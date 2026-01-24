nmap shows 22 and 8080, i start with http
its a jenkins server, i look around as unauthenticated user, and notice there is a user "jennifer"
![[Pasted image 20250918205614.png]]
the version of jenkins is 2.441, i google for vulnerabilities, there is a file inclusion / file read vuln, i mess with it a bit, and decide to run a bruteforce for jennifer (couldnt find anything else relevant, and authenticating should let me elevate this vulnerability to full file read)
brute force works, the password is princess - as jennifer i can run groovy scripts on the host using jenkins
i google for a groovy revshell, modify the values and open a listener - and i get a foothold
i try shell stabilization, but no python installed, im probably inside a container.
while looking at mounted files i notice my flag
![[Pasted image 20250918210004.png]]
while grabbing my flag, i notice some interesting files in the same dir
![[Pasted image 20250918210119.png]]

inside the credentials.xml file, it seems there is a SSH key for root, but the format looks odd, so its probably encoded or encrypted somehow.
![[Pasted image 20250918210345.png]]
i try some bullshit like adding the SSH key headers myself but it doesnt work ofc

i google on how to decrypt jenkins creds, and i find a very helpful thread
![[Pasted image 20250918210423.png]]
i decide to try the same thing for the SSH key and it works
![[Pasted image 20250918210510.png]]

i login as root via SSH, and grab the root.txt flag
