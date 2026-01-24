start with nmap, find 445
check guest access, activated and can read "support-tools"
many known tools in the directory, 1 seems to be a custom tool in a zip file
download zip and extract, checked md5sum in VT to check if actually a known tool
someone in the comments gave the tip to decompile the exe
decompiled with VScode extension, found c# code to decrypt the password of "ldap" user
couldnt fix the code to run myself, used claude to do it
with ldap user creds, enumerate all ldap info on the domain
also get bloodhound files using ldap user
notice in bloodhound data that the best way to proceed is to get access to the "support" user (due to permissions and groups)
check ldap data entries for "support" the field "info" contains a string that could be a password
login as "support" and grab user.txt with WinRM
couldnt figure out the privesc method, need to read: file:///C:/Users/yoval/OneDrive/Desktop/Support.pdf
