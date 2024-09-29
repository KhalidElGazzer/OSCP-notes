**1. System Enumeration:**

* `systeminfo`: Retrieve detailed system information.
* `systeminfo | findstr /C:"OS Name" /C:"OS Version" /C:"System Type"`: Filter the output to display OS name, version, and system type.
* `wmic qfe`: Display a list of all installed Windows updates (Quick Fix Engineering).

**2. User Enumeration:**

* `whoami`: Show the current user.
* `whoami /priv`: Display privileges of the current user.
* `whoami /group`: List groups the user is a part of.
* `net user`: Display all users on the machine.
* `net user <username>`: Get detailed information about a specific user.
* `net localgroup administrator`: Show members of the admin group.

**3. Network Enumeration:**

*`ipconfig /all`: Get all network configuration details.
* `arp -a`: Display the ARP table (IP to MAC address mapping).
* `netstat -ano`: List all active connections and listening ports along with process IDs.
* `netsh wlan show profile`: List saved Wi-Fi profiles.
* `netsh wlan show profile <SSID> key=clear:` Display Wi-Fi password of a specific SSID in plaintext.

**4. Firewall & AV Configuration:**
* `sc query`: List all services running on the machine.
* `sc query windefend`: Check the status of Windows Defender (running or stopped).

Default Writeable Folders
--
```
C:\Windows\System32\Microsoft\Crypto\RSA\MachineKeys
C:\Windows\System32\spool\drivers\color
C:\Windows\System32\spool\printers
C:\Windows\System32\spool\servers
C:\Windows\tracing
C:\Windows\Temp
C:\Users\Public
C:\Windows\Tasks
C:\Windows\System32\tasks
C:\Windows\SysWOW64\tasks
C:\Windows\System32\tasks_migrated\microsoft\windows\pls\system
C:\Windows\SysWOW64\tasks\microsoft\windows\pls\system
C:\Windows\debug\wia
C:\Windows\registration\crmlog
C:\Windows\System32\com\dmp
C:\Windows\SysWOW64\com\dmp
C:\Windows\System32\fxstmp
C:\Windows\SysWOW64\fxstmp
```

Powershell History
---
Commands run in Powershell are saved in `ConsoleHost_history.txt`. To access it from `cmd.exe`:

```
type %userprofile%\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt
```
Saved Windows Credentials
---
View saved credentials using:
```
cmdkey /list
```
To use saved credentials:
```
runas /savecred /user:admin cmd.exe
```
IIS Configuration
---
Check for stored passwords in ```web.config```:

```
type C:\inetpub\wwwroot\web.config | findstr connectionString
```
PuTTY Stored Proxy Credentials
---
Retrieve with:

```
reg query HKEY_CURRENT_USER\Software\SimonTatham\PuTTY\Sessions\ /f "Proxy" /s
```
