we started with trying to enum SMB, it has nothing for unauthenticated user / Guest, so we move to enum http
in the website in front of me, there isnt much at all, but the main page features 2 pdf files that i can download, they are filled with latin garbage so these files are not important, but both have the same name format, so its a hint that IDOR might be possible
using ffuf, i can very quickly and easily scan every date in the radius of 5 years to both directions, first i make 3 wordlists, years from 2015 - 2025, months from 01 to 12 and days from 01 to 32
then this command quickly scans all the thousands of potential files
`ffuf -u http://10.129.99.112/documents/ZFUZZ-HFUZZ-WFUZZ-upload.pdf  -mode clusterbomb -w months:HFUZZ -w days:WFUZZ -w years:ZFUZZ -v | grep "http"`
from my terminal, i copy the list of results, and quickly get rid of anything that isnt the URL using find and replace in text edtior, now that i have a list of all the pdf files in the directory, i check each of them manually, until i find the pdf that contains the initial password for new accounts on the domain
i also wanted to check if any data is embedded in these pdf's so i downloaded 1 and ran exiftool on it and saw that exiftool shows the 'creator' of the PDF
![[Pasted image 20251006163448.png]]
i tried the username in impacket-getPNUsers, just to test if its a real user, and i figured it is, since the response was that this user doesnt have the flag set
so i downloaded all the pdfs to a directory, using wget with a argument for downloading all the links in a list, and i ran a tool called pdfxplr.py to get the results of all the pdfs in the directory
![[Pasted image 20251006163823.png]]
then i made a list of users, and attempted to spray the password i got against this list, using nxc smb - found that only 1 account works, and it is Tiffany.Molina
and this user has access to 2 SMB shares, Users, and IT - in the IT share, there is a powershell script that checks if the web servers are up and i know it was made by someone named "Ted" (it was information i found in 1 of the PDF files)
i grab bloodhound files with bloodhound-python, and look around at interesting users, and i notice that getting hold of the user 'Ted.Graves' will allow me to obtain many more privileges in the domain.
![[Pasted image 20251006164310.png]]
seeing this, i highly suspect that the powershell script is my way to proceed, examining this script, i notice that it does make use of the current user credentials
![[Pasted image 20251006164539.png]]
i didnt really understand this script by myself and used ChatGPT, but i did understand the exploitation method, i need to control this dynamic parameter to make the script attempt authentication to HTTP server that i control
the solution to this is dnstool.py 
![[Pasted image 20251006165422.png]]
all i had to do is add a new DNS record with 'web' in the title, that leads to my own machine, and just open a responder on tun0 and wait
![[Pasted image 20251006165542.png]]
i copy it all into a file, and bruteforce with john - i find the password is Mr.Teddy
Now i need to abuse the ReadGMSAPassword privilege, and thats easy enough - i clone and install gMSADumper.py and run it to retrieve the hash for svc_int$
![[Pasted image 20251006165752.png]]
now that i control the svc_int$ account which is allowed to delegate to WWW/dc.intelligence.htb, i just need to get a ST while impersonating Administrator, and use it to authenticate and get a shell using impacket-wmiexec
![[Pasted image 20251006170010.png]]
![[Pasted image 20251006170043.png]]