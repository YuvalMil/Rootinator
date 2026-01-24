starting enum with nmap, not a typical host 
![[Pasted image 20251027122324.png]]
cant connect to 80 at all, moving to 8000 - cant find any interesting endpoints, while looking at the headers in the response i notice an interesting header
`X-Upstream: salt-api/3000-1`
![[Pasted image 20251027122424.png]]
i try to google the header and i immediately land on the needed exploit
![[Pasted image 20251027122509.png]]
copy the code, install dependencies in venv, and run with curl to confirm RCE
![[Pasted image 20251027122659.png]]
tried many different approaches and couldnt get a revshell, even tried disabling firewalld but still wouldnt work
the solution was to try port 4505 which is used on the host to run the ZeroMQ server, and therefor isnt blocked by any firewall rules / restrictions 
![[Pasted image 20251027122957.png]]
