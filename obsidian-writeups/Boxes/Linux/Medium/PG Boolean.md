starting with nmap, 22,80,33017
starting with HTTP enum on port 80, nmap scan shows that 33017 is also HTTP service, ill try to enum it too if i hit a wall
the index page is a login portal, there is also a register hyperlink under it, i quickly create a user and login, after logging in it seems the app wants me to confirm my registered email by clicking a link that was sent to it, also had an option to change my registered mail post registration, tried some SSRF fuzzing which didnt work, also tried some SSTI and SQLi since i had reflection of my input, and i thought the function might update the DB, these didnt work. 
![[Pasted image 20251103184526.png]]
inspecting the post request in burp shows user parameters in the response
![[Pasted image 20251103184759.png]]
noticed the parameter is `user[email]`, i tried to change the `email` to `id` and noticed that the order of the fields changed in the response.
![[Pasted image 20251103184700.png]]
![[Pasted image 20251103184815.png]]
tried to change `id` to `confirmed` and it actually changed the value from false to true
![[Pasted image 20251103184852.png]]
after the registered account is confirmed, the page changes to a file manager
![[Pasted image 20251103185005.png]]
uploaded a random file to test the behavior, and check for any restrictions
![[Pasted image 20251103185055.png]]
clicking on the file will start a download, but also reveals a few important URL parameters
![[Pasted image 20251103185127.png]]
wasnt sure whats cwd, tried to google it, seems like it could be `current working directory`
i tried some basic LFI techniques, wanted to display /etc/passwd
tried some different variations, eventually i tried path traversal in the cwd parameter, which worked and displayed passwd
![[Pasted image 20251103185330.png]]
after some more messing around, i found out that the file manager will display the contents of the directory if i keep the file parameter empty
![[Pasted image 20251103185432.png]]
using this to check the home directory, and check for a user's .ssh directory:
![[Pasted image 20251103185526.png]]
![[Pasted image 20251103185538.png]]
![[Pasted image 20251103185644.png]]
im in .ssh but there is no authorized_keys file, that could be convinient for me, if the current user has permissions to upload inside this directory, i can just upload a public key called `authroized_keys` and then ssh into the host as `remi`
to test this, ill generate a key pair on my kali machine, and then change the public key name to `authorized_keys` before i try to upload it into the .ssh directory using the web file manager.
![[Pasted image 20251103185910.png]]
it worked, i should be able to ssh into the machine now
![[Pasted image 20251103185942.png]]
while looking in the .ssh directory, i noticed a key file called `root` which is interesting
![[Pasted image 20251103190035.png]]
theres also an alias on the machine that seems very interesting
![[Pasted image 20251103190117.png]]
but when i try to use that alias it doesnt work for some reason 
![[Pasted image 20251103190133.png]]
after 3 hours of wasting time, i look for the answer and find out that by default, the SSH agent when trying to authenticate with an identity file (key), wont use only the key that was specified, it will try every single identity file it can find, and on certain servers, the limit that is set on authentications attempts could be set to a lower number than the available identity files, leading to authentication attempts that dont even use the specified file, and thus end up failing.
to get around this retarded behavior, it is necessary to use the argument `-o "IdentitiesOnly=yes"`

knowing this, we use the command `ssh -o "IdentitiesOnly=yes" -i ~/.ssh/keys/root root@127.0.0.1` and get a root shell
![[Pasted image 20251103190541.png]]
