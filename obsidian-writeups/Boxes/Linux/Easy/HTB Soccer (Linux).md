nmap shows 9091 and 80
start with 80, index page is a football club bullshit page with text only, to proceed we need to enum dirs and find the endpoint /tiny
which leads us to TinyFileManager, searched for the default creds on google, found it and it worked.
i log in, looked for some exploits for a bit, but decided to try to wing it since its a file manager
decision paid off, i have a dir with permissions to upload, and its not blocking PHP web shells, so i get a foothold pretty fast
manual enumeration on this host doesnt lead me anywhere, i decide to run linpeas
with linpeas output i find out about another site in nginx, a subdomain to soccer.htb, which was soc-player.soccer.htb, i mess around with that site for a while, there not much to check, the only real lead is this websocket at port 9091 which seems to handle some DB tasks, as it checks for tickets validity on the site.
using the box guided mode, i confirm that SQLi in the websocket is indeed the way to proceed, i found a sweet tool on github which works as a proxy for SQLmap, that way i can launch SQLmap on a local port, and have the python proxy direct it to the web socket.
takes a while since the only injection point SQLmap found was a boolean injection, but i end up with the "player" user clear text password, i log in with ssh, grab user.txt
while doing enum on the machine as www-data, i already found the path to root as 'player' on the host the binary "doas" is installed with SUID bit, and owned by root
when i noticed that, i did some reading and found out that this binary can only be used according to the config file, so i read that and found that player is allowed to run dstat as root with no pass requirement
at this point it was a quick check in GTFObins to find the way to escalate privileges with sudo dstat, which was creating a shell file in the directory that allows for importing dstat modules, by executing the fake module as root, i immediately popped a root shell, and grabbed the flag.
