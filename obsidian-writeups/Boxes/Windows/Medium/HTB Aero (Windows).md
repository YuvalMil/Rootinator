only 1 port, HTTP - website only has 1 function, a file upload for .theme or .themepack files
after a while of enumerating, i decide to look specifically for RCE through theme files, because i dont see any other way to proceed, i find a very interesting [exploit](https://github.com/Durge5/ThemeBleedPy) for  CVE-2023-38146 - the first one i found had to be compiled and shit so i just looked for a python version, although in this python version the stage_3 file is only popping a calc, its very easy to generate a revshell DLL using msfvenom and replacing the calc payload with the msf 1.
i also swapped the slashes in the theme file where the attacker IP is specified, because the POC uses backslashes, which doesnt work with my kali
my theme file:
![[Pasted image 20251007222806.png]]
POC theme file:
![[Pasted image 20251007222828.png]]
after i placed my revshell payload, and fixed the theme file, i just need to open the python wrapper that uses impacket-smbserver, and open a listener for my revshell
![[Pasted image 20251007222937.png]]
so with my foothold i grab user.txt, and because there is only 1 open port, i immediately check the wwwroot directory, but there is nothing interesting there.
i start to check the compromised user directories, and in the documents directory i find a eye catching PDF
![[Pasted image 20251007223114.png]]
after googling i find that this CVE is a priv esc vulnerability in windows 11, and i also noticed that there is an exploit on msf, so i quickly create a new msfvenom payload for a meterpreter shell, download it to the target host with curl, catch it in msf with multi/handler, and run the exploit
![[Pasted image 20251007223315.png]]
