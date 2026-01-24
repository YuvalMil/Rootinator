nmap shows only 22 and 80
on 80, a webapp with very few functions, only 2 - one is to submit a book publish request, and 1 is to upload a book cover
the upload function is vulnerable to SSRF, by scanning internal http services we find port 5000 which leaks a password to user 'dev'
as 'dev' we have access to a .git repo, i download the repo to my host, and use GitTools extractor to make the .git objects readable, i then use a simple grep -i -r "prod" to find any mentions of the prod user (the other user with a directory in /home)
found prod password in the repo, checking sudo -l shows a python script
![[Pasted image 20250916215748.png]]