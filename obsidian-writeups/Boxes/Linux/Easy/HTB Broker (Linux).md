nmap shows many ports, its just noise, started checking 80 http
its ActiveMQ server, asked for username and password, admin admin worked
looked around, nothing interesting, saw copyright 2005-2020 - old version
found Unauthenticated RCE for ActiveMQ and the CVE is 2023, looks promising
wasted a lot of time messing around instead of reading the exploit, in the end used the MetaSploit module, i had to put random number in the payload settings because both the exploit and the payload try to host the same port
checked sudo -l as activemq user, shows passwordless sudo nginx with no limitation on arguments, GTFObin didnt show anything, but googling about sudo priv esc with nginx, i found an exploit that creates ssh key pair, and uses the sudo nginx permissions to host it locally, and a specially crafted config file that allows to edit the directory with PUT requests, which allows to add the new key to roots authorized keys
![[Pasted image 20250911024944.png]]
copied the key, changed permissions to allow auth, and logged in as root.
