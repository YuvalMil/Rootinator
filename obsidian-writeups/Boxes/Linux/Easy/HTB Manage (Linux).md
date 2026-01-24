not a typical box, running some java services that im not familiar with
![[Pasted image 20251026205250.png]]
read some blog posts and guides about enumerating these services

`found creds with beanshooter`
`java -jar beanshooter-4.1.0-jar-with-dependencies.jar enum 10.129.234.57 2222`
![[Pasted image 20251026204404.png]]
to get revshell with beanshooter:
1. confirm RCE with curl
2. create a txt file that contains the revshell payload, in this case a simple bash 1liner from revshell.com
3. host the txt file with python http server, use beanshooter to curl the txt file and add `-o /tmp/txtfilename` or in another dir location that is writeable
4. use beanshooter to execute the file with bash `bash /tmp/shell3`
`RCE with beanshooter`
![[Pasted image 20251026204324.png]]
there are 2 new users in `/home`, `useradmin` and `karl`, in `/home/useradmin` there is a backup file that is world readable, i copy it to tmp and untar it, it contains the entire home directory of that user, including the `.ssh` folder and the `.google_authenticator` file, this allows me to get access as `useradmin` and after i confirm it, i take some time to mess with the authenticator abit to make my life easier 
`remaking the google authenticator file to allow reuse`
![[Pasted image 20251026204617.png]]

`sudo -l`
`(ALL : ALL) NOPASSWD: /usr/sbin/adduser ^[a-zA-Z0-9]+$`
in ubuntu the user `admin` has full sudo privileges, on this host the user admin doesnt exist
![[Pasted image 20251026211945.png]]
![[Pasted image 20251026211956.png]]
