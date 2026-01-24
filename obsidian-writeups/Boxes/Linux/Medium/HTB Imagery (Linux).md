needed to find webapp at port 8000
notice a small button on the bottom of the page for reporting issues
![[Pasted image 20250929193048.png]]
test it and notice its really sending a post request
![[Pasted image 20250929193131.png]]
says admin review is in progress, i check cookies and notice session cookie is HTTPonly = false
i try XSS payload to steal admin cookie - it works
login as admin and look around, new function is visible in GUI, i test it, the parameter in the request is vulnerable to LFI
![[Pasted image 20250929193311.png]]
i find config.py, which leads me to db.json, and i get creds for a test user with additional functions unlocked
![[Pasted image 20250929193404.png]]
![[Pasted image 20250929193431.png]]
hash is md5 for "iambatman"
###### WHAT I MISSED:
###### if i looked for app.py, i had the potential of finding the imported libraries, which would lead me to inspect api_edit 
![[Pasted image 20250929193810.png]]
in api_edit there is a command injection vulnerability in the crop function
![[Pasted image 20250929194100.png]]
i controll the x, y, width, and height params, so its a very easy command injection once you find this api file
![[Pasted image 20250929194208.png]]
now i get foothold as 'web', in the /var/log dir there is only 1 file, its a zip.aes file so its encrypted but world readable, so i can copy it to the uploads directory on the python webapp, and download it from the browser
to attempt to decrypt this file, i need to write a python script that uses PyAesCrypt, and attempts all passwords from a wordlist, but because i already failed this box, and i wouldnt be able to write this script myself anyways, i use AI to generate a script and crack the password
in the archive i find another db.json which contains the password for mark, its an md5 for "supersmash"
mark can run sudo charcol, i run it to mess with it abit, its a backup program.
i try to backup /root and /root/.ssh, but it blocks me, the binary refuses to backup these dirs cause its a sensitive dir, so i attempt to add a cronjob managed by charcol, i try with simple bash revshell payload, it doesnt work
i try again with a base64 encoded payload, it works and i get revshell as root.
