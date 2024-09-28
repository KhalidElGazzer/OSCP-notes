Much of Linux privilege controls rely on controlling the users and files interactions. This is done with permissions. By now, you know that files can have read, write, and execute permissions. These are given to users within their privilege levels. This changes with SUID (Set-user Identification) and SGID (Set-group Identification). These allow files to be executed with the permission level of the file owner or the group owner, respectively.

You will notice these files have an “s” bit set showing their special permission level.

This will list files that have SUID or SGID bits set.

```
find / -type f -perm -04000 -ls 2>/dev/null
```

If we exploit this we could take a copy from ```/etc/passwd``` and ```/etc/shadow``` to crack both of them.

First we need to unshadow those files:

```
unshadow passwd.txt shadow.txt > finall.txt
```
Then, john comes:
```
john --wordlist=/path/to/wordlist.txt final.txt
```
The other option would be to add a new user that has root privileges. This would help us circumvent the tedious process of password cracking. Below is an easy way to do it:


We will need the hash value of the password we want the new user to have. This can be done quickly using the openssl tool on Kali Linux.





















