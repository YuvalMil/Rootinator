started with nmap, only 22 and 80
checking http, looks like a web app with a "check if site is up" function
tested for SSRF and other stuff, while dir searching found /dev dir 
tried to scan /dev dir, found a open git repo
used git dump to recreate all the php files, found the access check for the dev subdomain. which was a header "Special-Dev: only4dev"
on the dev subdomain there is also an upload function that i need to abuse by reading the source code
by reading the php source code i could identify a few things:
the file extension check could be bypassed by placing an extra dot, since the check worked by reading the string after the LAST dot in the file name
the "page" query parameter used a vulnerable include function, which allowed for LFI with php wrappers
the php code instantly deletes the uploaded file from the /uploads directory after reading the content
i also managed to execute php code using a filter chain generator, and spent many hours trying to escalate it, but the server max query length kept blocking me, and it ended up being the wrong way to proceed
the solution i missed:
what i should've noticed is that the script tries to read the file contents before deleting it, and thats why uploading a phar or zip file will avoid being instantly deleted - the script tries to read the binary data with the file_get_contents function, and this most likely crashes the script mid execution, preventing the deletion of the file.
with all of this out of the way, all thats left is to read the phpinfo page to find disabled functions, or use dfunc-bypasser to automate the process (find out which functions are disabled to find a legitimate way to execute code)
dfunc-bypasser find out that proc_open is not disabled, i upload a revshell using proc open, the revshell is rev.php, inside a zip rev.zip which is renamed to rev.txt, i can then use the LFI at the page parameter with the phar:// php wrapper to execute the content inside the zip, and i get a revshell
as www-data, i check the /home directory, notice there is only 1 dir, for the user developer, in the dir there are 2 files, a ELF binary, and a python script, after messing with the files for a bit, i figured out that the ELF binary is calling the python script when executed.
i should also note that the elf binary has SUID flag, making it run as the user who owns the file - "Developer"
i dont have read permissions, but the group www-data owns the directory, so i can copy the files out of the developer home directory into /tmp, and then cat the python script
what i shouldve noticed here is that the script is using python2, and using the input function, which according to the walkthrough has the same vulnerability as eval function, and allows for code execution. (i did notice python command injection, when i tried to put open function as input, but didnt understand how to take it a step further)
by inserting this payload as input, i got a shell as the file owner 
`__import__('os').system('/bin/bash')`
from there it was a simple matter, taking private ssh key from .ssh directory, logging with ssh, running sudo -l
![[Pasted image 20250910194313.png]]
checked gtfobin, easy_install with sudo means instant priv escalation to root
