SSH and HTTP open
Starting with HTTP enum, found a webapp that receives a URL and checks if the site is down or not
Sending a request to myself with open nc listener, i find the user agent is curl
curl is able to process few urls with space between them, trying to send few urls in the webapp allows to bypass security restrictions like protocol check, and leads to local file read with `file://`
by reading the index.php file, i find that a get parameter `expertmode=tcp` will change the functionality of the webapp, the new function will receive an IP and port, and check if that port is open for communication
by adding `-e /bin/bash` after the port number, i make the webapp connect to a reverse shell instead of only checking the port connection.
after gaining foothold as `www-data` a password vault called `pswm` can be found on the user `aleks` home directory
the pswm vault can be bruteforced with a github tool, and by cracking the vault with a rockyou.txt im able to find the users password
`aleks` has permissions to use ANY sudo command, allowing instant privilege escalation to root
