couldnt solve anything on my own, solved with walkthrough
after nmap, only 80 and 22 are open
on 80 a web application that looks custom, after registration and login, the only function with user input is the option to select the user favorite genres, which will change the photos in the feed section of the app (its a photo gallery app)
this scenario hints at a second order SQLi, and sqlmap can be used here to fetch the whole DB
![[Pasted image 20250911072002.png]]
i saved both POST and GET requests from burp, in the first one we update our user genres, and the second one is requesting the feed data from the server
its worth noting that this wouldnt work without specifying the --tamper=space2comment flag, its pretty obvious that the server is removing any white spaces in the genres value
another point to add: i let sqlmap test with a valid value in the parameter, it seems to be necessary
after a while sqlmap identifies an injection point 
![[Pasted image 20250911072226.png]]
once it does, it will save the necessary data in a local file, that way it can dump the whole DB in seconds while using the same injection point
![[Pasted image 20250911072636.png]]
![[Pasted image 20250911072705.png]]
with this we get the hashes for all users, but these a Bcrypt hashes and should be pretty much uncrackable, so the walkthrough starts to pivot elsewhere
the next step was to enumerate the API endpoint,  since we already had /api/v1 , it was only natural to attempt enumeration for /api/v2 as well
if i had tried to copy the login api endpoint at /api/v1 to /api/v2, i wouldve found a POST only endpoint, which requires "email" and "hash" as body parameters
by supplying the first set of credentials from the compromised DB, i couldve authenticated as admin on the application
next, we are given some clues, we are told that the application is using PHP, and we are told about a new feature, which allows for editing pictures and applying filters
while checking out this feature, its noticeable that a POST request is sent to a /api/v2 endpoint called /modify, by inspecting this POST request, i could uncover a RFI vulnerability in the application
![[Pasted image 20250911080054.png]]
finding this RFI tho wouldnt give me anything alone, the real challenge here wouldve been to find this article: 
https://swarm.ptsecurity.com/exploiting-arbitrary-object-instantiations/
which i shouldve found by searching a way to exploit Imagick
by followingthe steps in the walkthrough, i managed to use the exploit, and get a webshell on the server
but even after the webshell is functioning and giving correct output, its still not possible to get a revshell with a normal payload, in order to get a revshell here, we create a file that contains a oneliner bash revshell, and inject a curl command from the webshell, into the hosted revshell, and add |bash at the end to make the target host execute the contents of the hosted file
![[Pasted image 20250911080544.png]]
finally, we get a foothold as www-data
by looking around in the app directory, i should notice a .gitignore file, which means i can attempt to retrieve the repo logs, but by trying git log -p, i get an error because the repo is owned by root, even if i try the exception argument, i get permission denied.
this is bypassable by changing our user $HOME environmental variable, since thats where git gets the configuration from
![[Pasted image 20250911081002.png]]
its not possible to read the repo log, but its way too long to read manually, what i chose to do is save the log output to a file on /tmp, and then copy the file into a public dir that is hosted on the server, that way i can watch it in the browser and use F3 for easy searches
doing that, its very easy to find the password for the user "greg", this also gives me SSH access as greg.
first thing i do after logging in as greg is checking groups, and checking sudo -l
groups command shows that greg is also in the group "Scanner", this is definitely intentional, -sudo l shows nothing, but in gregs home folder there are interesting files
dmca_check.sh and dmca_hashes.test, since its in my user home directory, i can copy the sh script to /tmp and then inspect it, which shows the use of a binary at /opt/scanner/scanner
the scanner binary is called with another file as argument, but to that file my user has no permissions, not to the file itself and not to its directory
but when i run the script, it runs without fail - this hints that something is in play here, either capabilities, or SUID set for the binary
after checking both, it seems that the scanner binary has full file system read capabilities /opt/scanner/scanner cap_dac_read_search=ep
by calling the scanner binary with no arguments, we can see the help page that shows the possible arguments and options

explanation of priv esc using the scanner binary:
![[Pasted image 20250911081903.png]]
basically, the author of the walkthrough created a python script that will map the whole private ssh key in root's folder, by checking each byte separately, comparing byte by byte to every printable character until a match is found, this basically allows to map the contents of every nonbinary file on the system
![[Pasted image 20250911082417.png]]
root's private key is printed to the terminal, i save it locally, change permissions, and log in as root to grab the flag.
