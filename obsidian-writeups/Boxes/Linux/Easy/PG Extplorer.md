starting with nmap scan, only HTTP and SSH are open
starting with HTTP, index page is showing a wordpress install that isnt finished, doesnt seem like i can do much from the install page itself, moving to dir brute force
dir bruteforce found `/filemanager` endpoint, which leads to `extplorer` login page, tried `admin:admin` and got admin access to the file manager
uploaded a php webshell, and used a php payload to get a revshell
looked in `/home` and saw the other user is called `dora` , in the filemanager source files, i tried `grep -r -i "dora"` and with that found the hashed password for dora's filemanager account, cracked the hash using john and found `dora:doraemon` 
ran linpeas as dora on the host, and noticed something interesting
![[Pasted image 20251106151308.png]]
the disk group is highlighted, so i started researching why and found an excellent [post](https://www.hackingarticles.in/disk-group-privilege-escalation/) explaining this priv esc method
first we run `df -h` to take a look at disk partitions
![[Pasted image 20251106151438.png]]
then we want to target the partition thats mounted on the root of the file system, in this case its `/dev/mapper/ubuntu--vg-ubuntu--lv`
so we run `debugfs /dev/mapper/ubuntu--vg-ubuntu--lv`
![[Pasted image 20251106151540.png]]
then we test permissions with mkdir, it is read only - we can try to look for a ssh key for root, but there isnt any, the only option left that is viable by default is reading shadow and passwd and try to crack it
`cat /etc/shadow`
`cat /etc/passwd`
copy and create both locally
`unshadow passwd shadow > unshadow`
`john unshadow --wordlist=/usr/share/wordlists/rockyou.txt`
![[Pasted image 20251106151912.png]]
