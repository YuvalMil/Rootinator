nmap shows http at 80 and 3000, nothing in SMB
at 80, a 1 page website, no backend functions
at 3000, a Gitea server with 1 public repo
check commit history to the public repo, the owner "ellen.freeman" forgot to remove his API key to gitea in the first commit
use API key to check other repos, find a repo called website, the contents match the website on port 80
use the API key and documentation to upload aspx webshell (website on port 80 shows asp.net backend in HTTP response)
gain foothold with the webshell as ellen.freeman, notice in his documents folder a config.xml file, it seems to contain encrypted creds
find a tool online to decrypt the config file (had to search for a tool specifically for config files of  mRemoteNG)
get creds for second user, grab user.txt
login with RDP, notice a tool called PDF24 on the desktop
checked other common vectors, no findings
check for PDF24 priv esc vulnerability, found  CVE-2023-49147
followed the instructions and ended up with system privileges
grab root.txt from administrator desktop
