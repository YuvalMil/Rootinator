nmap scan reveals SSH and HTTP
started with http enum, dirsearch revealed `/login` and `/register` so i create an account and login
![[Pasted image 20251106205744.png]]
the image upload doesnt seem useful (after testing), but the debug mode is interesting, since it reveals a lot of information when getting an error
![[Pasted image 20251106205908.png]]
also 404 error shows the tech stack
![[Pasted image 20251106205928.png]]
a bit of searching later, found a RCE exploit for laravel when debug is enabled - [CVE-2021-3129](https://github.com/joshuavanderpoll/CVE-2021-3129)
this allowed me to get a working revshell as www-data
after about 1 - 2 hours of enum, i decide to uploald pspy to the host, to look for any automated actions, and i find an interesting command that keeps repeating every 1 minute
![[Pasted image 20251106210224.png]]
using pspy its very obvious, there is a cron job that calls laravel artisan with a custom command `clear:pictures` and that command runs `rm -Rf /var/www/html/lavita/public/images/*` 
after a quick google search i find out that artisan custom commands can be found in the directory `app/Console/Commands` in the project root dir
in there i find the file `ClearCache.php` this file is writeable by my user `www-data` as well as the dir itself.
on my kali machine i create my own `ClearCache.php` but i change the `rm rf` command with a revshell payload

Original file:
![[Pasted image 20251106210608.png]]

My custom made file:
![[Pasted image 20251106210621.png]]

then i move the old `ClearCache.php` to /tmp and wget the new file with the revshell payload
after a minute, my listener receives the connection from the target host, and i am now the user `skunk`
checking `sudo -l` as skunk reveals this privilege:
![[Pasted image 20251106210734.png]]
all i have to do is follow the instructions on GTFObins
`TF=/var/www/html/lavita` - setting variable TF as our allowed to use dir
`echo '{"scripts":{"x":"/bin/sh -i 0<&3 1>&3 2>&3"}}' >$TF/composer.json` - rewriting composer.json to host our payload
`sudo /usr/bin/composer --working-dir\=/var/www/html/lavita run-script x` - running the composer as sudo and executing the payload
![[Pasted image 20251106211058.png]]
![[Pasted image 20251106211115.png]]