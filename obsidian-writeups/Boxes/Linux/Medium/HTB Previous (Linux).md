after nmap i start with http enum - i find absolutely nothing
its easily noticeable that this is a next.js project, with some more digging its possible to identify that nextauth.js is running on the server
after googling abit i find a [authorization bypass](https://projectdiscovery.io/blog/nextjs-middleware-authorization-bypass) in  nextuauth.js, and confirm it works on the target. using the bypass, im trying to look around beyond the login screen
by looking around in the now open docs, i find an API endpoint that looks suspicious
![[Pasted image 20251011003751.png]]
i check it for LFI and it works
its possible to check /proc/self/environ to see the PWD
the most crucial thing for locating the password with the identified LFI is to know that the `routes-manifest.json` file is in the `.next` directory by default.
![[Pasted image 20251010221142.png]]
having access to the routes file, i can see that the auth file is under /api/auth/`[...nextauth]`/  

![[Pasted image 20251010221435.png]]
only by knowing next.js project structure its possible to conclude that the source code for the backend will be under /.next/server/pages/
and it leads to a first set of credentials and a foothold
im now connected using SSH as jeremy, running sudo -l shows that i can run a very specific terraform command
![[Pasted image 20251011004251.png]]
it seems that the apply command is checking for configuration from a file, and applying these configs to a destination location
it appears that there is a dev option in terraform that allows to bypass the source config file, with a env variable called `TF_CLI_CONFIG_FILE` , with that variable, i can create my own config file, that will change the installation file used for the terraform "provider"
this is the file terraform is using currently
![[Pasted image 20251011004548.png]]
here is the malicious config file i created `test.tfrc`
![[Pasted image 20251011004646.png]]
`/tmp/exploit` is the directory containing a simple shell script to add SUID to /bin/bash
at first i failed because i didnt know there is a name requirement
![[Pasted image 20251011004828.png]]
and after fixing the name i still failed because i forgot to add a shebang, so it was regarded as a text file and not a executeable

![[Pasted image 20251011004928.png]]
after fixing both, i got another "error" but it wasnt really a failure
![[Pasted image 20251011005003.png]]
now `/bin/bash -p` gives me a privileged shell, i can read the flags as jeremy in this shell, but if i wanted root user itself, i could grab id_rsa to easily switch users
