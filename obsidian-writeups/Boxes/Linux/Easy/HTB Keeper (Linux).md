nmap shows only 80 and 22
on 80 there is a index page that hints at a subdomain tickets.keeper.htb
on that subdomain, a system called Request Tracker is installed, im able to login as root by searching the default creds on google, root:password 
after looking around, i find a ticket on the system that contains ssh credentials
when checking the home dir of the user, i find a zip file that contains a keepass db, and a keepass dump file
after reading online about how to read dump files, i decided to change approach and look specifically about keepass dump files, thats when i stumbled upon CVE-2023-32784 which allows to find a keepassdb master key from a dump file
had some issues finding the password using the exploit, but after trying to google a part of the output, i found the full name of a foreign dish, that ended up being the master key
the keypassDB had a putty rsa key for root, i copied the putty key to a file, and used puttygen to convert it to a normal openssh key, and used it to login as root
