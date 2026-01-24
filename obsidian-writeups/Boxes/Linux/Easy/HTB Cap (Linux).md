only 3 ports, ssh http and ftp
no anonymous access for ftp, empty username and password doesnt work, moving to http
the http server is running an application for data monitoring, there is also a panel that allows to download a pcap file
i download the pcap file and check it out, it seems void of any relevant info, looking at the website again, i notice that IDOR might be possible
![[Pasted image 20251025170054.png]]
in `data/1` there are 626 packets to analyze, when changing to `data/2` it changes to 0
![[Pasted image 20251025170134.png]]
i check `data/0` - there are packets to analyze, i download the new pcap
![[Pasted image 20251025170206.png]]
found FTP traffic with a set of creds
![[Pasted image 20251025170239.png]]
FTP only contains the user flag so its not for escalation
![[Pasted image 20251025170318.png]]
the room is called cap, so i thought maybe the escalation will be around capabilities, and it was

![[Pasted image 20251025170409.png]]
![[Pasted image 20251025170423.png]]
