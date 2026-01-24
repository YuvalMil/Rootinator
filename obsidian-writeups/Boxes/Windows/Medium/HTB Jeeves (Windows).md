nothing on SMB, on 80 theres only a error picture and theres also 50000
after a while of enumerating and finding nothing, i try http://target:50000/askjeeves and it actually exists, i get a jenkins page that is completely open, no auth needed - i can get a foothold easily through the jenkins groovy script runner
after foothold i find a keepass file in documents directory, i easily crack the file with keepass2john, and in there i find the NTLM hash for administrator, but the root.txt file doesnt exist, and the flag is somewhere else
its for sure something to do with a user 'bob' that name is not one of the users on the host, but it shows in the registry in the autologon section, and in unattend.xml file, and also in the unlocked keepass
in the end it was a simple data stream trick, which can be revealed by doing dir /r 
![[Pasted image 20251007024214.png]]
the way to read the flag is by directing the output to more, which reflects it to my terminal.