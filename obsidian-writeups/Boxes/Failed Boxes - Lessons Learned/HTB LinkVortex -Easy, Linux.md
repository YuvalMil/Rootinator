failed completely here, found the public git repo on the dev subdomain, but didnt know i could do git status and git restore to look at changes
![[Pasted image 20250919015048.png]]
also tried the priv esc for a bit and couldnt do it
i had sudo permissions to run a script 
![[Pasted image 20250919015401.png]]
script receives a png file in argument and checks if its linked to something, if it is linked, it checks if the parent dir is /etc or /root, and if it is, its removing the link, otherwise its moving the linked file to quarantine and also reading the content if the CHECK_CONTENT variable is true
