nmap shows 22 and 80, i start enumerating http
dirsearch finds public git repo, i download it using git-dumper
i find the db password in settings.php, and after a while also find a valid username / email to the login page (Backdrop CMS)
i waste a lot of time instead of instantly try the valid email with the DB password but it works and i get authenticated
i try to find an exploit online for this version, and find an authenticated RCE exploit, sadly it doesnt work
eventually i manage to make it work by replacing 1 line of code and importing another library 
![[Pasted image 20250919050716.png]]
the original function os.getlogin() is troublesome after some googling i replace it with getpass.getuser()
![[Pasted image 20250919050823.png]]
this gets me a webshell, which i transfer to a revshell with rlwrap
i try the previous password on the user johncusack, and it works so i get user.txt
i check sudo -l, user is allowed to run Backdrop CMS "bee" as root
i google about bee priv esc, and find a quick poc
![[Pasted image 20250919051030.png]]
instantly lands me in a root shell and i can grab root.txt