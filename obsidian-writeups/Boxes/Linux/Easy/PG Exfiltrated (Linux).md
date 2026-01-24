starting enum with nmap, only http and ssh are open, i start http enum and immediately discover that it is vulnerable to authenticated RCE
![[Pasted image 20251028180838.png]]
![[Pasted image 20251028180910.png]]
i try some basic combinations, im able to log in with `admin:admin`
looked for a nice exploit to use, some worked, some only gave a psuedo shell and escalating to full interactive revshell proved difficult, i also tried manual exploitation by following [this](https://github.com/intelliants/subrion/issues/909), it worked and allowed me to execute commands on `index.php` by adding a `eval()` hook, but it wasnt very effective, eventually i found a really good [exploit](https://github.com/Drew-Alleman/CVE-2018-19422) that also allowed for file upload, i uploaded a php revshell file and executed it externally using the browser, finally i got a revshell as www-data
![[Pasted image 20251028181320.png]]
i failed at enumeration and missed the cron job ran by root at `/etc/crontab`
![[Pasted image 20251028181403.png]]
![[Pasted image 20251028181425.png]]
here i wasted precious time for attempting manual exploitation, only after trying different methods with file names i decided to look for a exiftool CVE that matches the version on the host
![[Pasted image 20251028181549.png]]
![[Pasted image 20251028181558.png]]
i read how the exploit works, and while it was probably doable manually, i wanted to find a nice POC on [github](https://github.com/convisolabs/CVE-2021-22204-exiftool) - which i did, and it worked flawlessly
![[Pasted image 20251028181723.png]]

i also had to edit the python script to add my ip and port for the revshell
![[Pasted image 20251028181922.png]]

![[Pasted image 20251028181738.png]]
i actually couldnt see the payload myself, so i just uploaded the generated file and hoped for the best
![[Pasted image 20251028181940.png]]
