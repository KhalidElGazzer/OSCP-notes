```
hostname
```
The hostname command will return the hostname of the target machine

```
uname -a
```
Will print system information giving us additional detail about the kernel used by the system

```
/proc/version
```
The proc filesystem (procfs) provides information about the target system processesLooking at /proc/version may give you information on the kernel version and additional data such as whether a compiler (e.g. GCC) is installed. 
```
/etc/issue
```
Systems can also be identified by looking at the /etc/issue file. This file usually contains some information about the operating system but can easily be customized or changed

```
ps 
```
Shows running processes.
Common options:
``` ps -A```: View all running processes.
       
```ps aux```: Show processes for all users, including those without a terminal.
```
env
```
Displays environment variables.
The ```PATH``` variable might include potential vectors for privilege escalation.

```
sudo -l
```
Lists commands the current user can run with root privileges.

```
ls
```
Lists files and directories.
Use ```ls -la``` to reveal hidden files and permissions.

```
id 
```
Shows the current user’s privileges and group memberships.

```
/etc/passwd
```
Contains a list of system users. Filtering for "home" can help find real users.

```
history 
```
Shows previously run commands, potentially exposing sensitive information.

```
ifconfig
```
Displays network interfaces and their configurations.

```
netstat
```
Provides network connection details.
Common options:
```netstat -a```: Shows all connections.
        
```netstat -l```: Lists listening ports.
        
```netstat -tp```: Shows connections with service name and PID.

find Command
---

Searching the target system for important information and potential privilege escalation vectors can be fruitful. The built-in “find” command is useful.

1- Find files:

    find . -name lol.txt: find the file named “lol.txt” in the current directory
    find /home -name lol.txt: find the file names “lol.txt” in the /home directory
    find / -type d -name config: find the directory named config under “/”
    find / -type f -perm 0777: find files with the 777 permissions (files readable, writable, and executable by all users)
    find / -perm a=x: find executable files
    find /home -user lol: find all files for user “lol” under “/home”
    find / -mtime 10: find files that were modified in the last 10 days
    find / -atime 10: find files that were accessed in the last 10 day
    find / -cmin -60: find files changed within the last hour (60 minutes)
    find / -amin -60: find files accesses within the last hour (60 minutes)
    find / -size 50M: find files with a 50 MB size

2- Writable and Executable Folders:

    find / -writable -type d 2>/dev/null
    find / -perm -222 -type d 2>/dev/null
    find / -perm -o w -type d 2>/dev/null
    find / -perm -o x -type d 2>/dev/null

3- Development Tools and Languages:


    find / -name perl*
    find / -name python*
    find / -name gcc*
4- File Permissions:

Find files with the SUID bit:
```
find / -perm -u=s -type f 2>/dev/null
```

Password enumeration:
---

```
$ history
```

SSH Key
----
look for ssh key :

```
find / -name authorized_keys 2> /dev/null
find / -name id_rsa 2> /dev/null
```
if we found SSH KEY Copy the key over to your local machine, and give it permissions (600)
```
chmod 600 root_key
```
use it to login:
```
ssh -i root_key_file root@<target ip>
```   
