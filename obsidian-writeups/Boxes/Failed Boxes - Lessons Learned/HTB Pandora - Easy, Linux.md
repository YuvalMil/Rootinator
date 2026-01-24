overall this box wasnt a complete failure, i managed to get to the priv esc part without any help, and i also identified the right vector to get root, i only failed at understanding restrictions imposed on my user.
when trying to run the SUID binary "Pandora_backup", it would fail due to not having permissions to /root dir, even tho the binary has SUID and owned by root.
also when trying to run sudo -l, i got an error 
![[Pasted image 20250918190329.png]]
i didnt recognize that this means a restricted shell, and i couldve escaped from this restriction using another SUID binary "at" 
![[Pasted image 20250918190517.png]]
![[Pasted image 20250918190534.png]]
summary: had to pop revshell > spawn pty > use escape payload > spawn pty again > continue with the PATH exploit