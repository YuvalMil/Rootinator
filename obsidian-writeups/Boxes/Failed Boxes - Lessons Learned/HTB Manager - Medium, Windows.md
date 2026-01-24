very bad attempt
1. i had a users list by brute forcing rid, and i tried all users with empty password or with username as password, but it didnt work, eventually i checked the answers and found out that one of the users had his password set to his username, but full lowercase
2. forgot i can use mssql to attempt to traverse the file system if xp_dirtree is enabled, i also failed to use impacket-mssqlclient because i forogt to attempt the argument -windows-auth
3. didnt realize its important to run certipy find again after gaining access to another user, different users have different permissions on templates and CA's
4. wasted a lot of time enumerating the website, even though it looked very minimal.