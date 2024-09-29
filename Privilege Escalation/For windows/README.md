Windows Privilege Escalation refers to exploiting system weaknesses or misconfigurations to gain higher privileges on a Windows system, typically from a low-privileged user (e.g., a standard user) to a high-privileged user like an administrator or even "System," which is the highest level of access on Windows systems.
Key Concepts in Windows Privilege Escalation:

**Privileges:**
* Standard User: Has limited access to files, processes, and system settings.
* Administrator: Can modify system files, install programs, and change system settings.
* System: Has complete control over the system, running with the highest privileges.

The goal of privilege escalation is to gain administrative or system-level access to the machine, allowing an attacker to perform actions that a normal user could not.






**Common Techniques for Windows Privilege Escalation:**
---
**1. Unquoted Service Paths:**

* Services in Windows often run with elevated privileges. If the path to a service executable is unquoted and contains spaces, an attacker can place a malicious executable in the path.
For example, if the service path is ```C:\Program Files\My Program\service.exe```, Windows will attempt to execute:
* ```C:\Program.exe```
* `C:\Program Files\My.exe`
* `C:\Program Files\My Program\service.exe`
  
If `C:\Program.exe` exists (created by an attacker), it will be executed with elevated privileges.

**2. Weak Service Permissions:**

* Services may have misconfigured permissions, allowing regular users to modify the service's executable, change its configurations, or even replace the executable file with a malicious one.
* If a user can modify a service that runs with SYSTEM privileges, they can replace the legitimate service executable with a malicious one and get SYSTEM access when the service is restarted.

**3. Insecure File Permissions (DLL Hijacking or Binary Hijacking):**

* If system executables or DLLs have weak file permissions, a low-privileged user can replace these files with malicious ones.
* When the executable or DLL is loaded, the malicious code will be executed with elevated privileges.

**4. Token Impersonation:**

* Windows uses access tokens to represent the security context of a process. Sometimes, privileged processes allow lower-privileged users to impersonate them.
  
* Example: If a service running as SYSTEM allows a standard user to create a thread that impersonates its token, the user can use the SYSTEM token and perform privileged actions.

**5. AlwaysInstallElevated:**

* If the `AlwaysInstallElevated` policy is enabled, MSI installers can run with elevated privileges. A low-privileged user can create a malicious MSI installer and use it to escalate privileges.

**6. Scheduled Tasks:**

* Attackers can abuse Windows Task Scheduler if they find scheduled tasks that run with high privileges but can be modified by low-privileged users.
* By changing the task to execute a malicious script or program, an attacker can achieve privilege escalation.

**7. Registry Permissions:**

* Misconfigured permissions on certain registry keys (especially related to services) may allow an attacker to modify values that control privileged processes.
* Changing the binary path of a service in the registry, for example, can lead to privilege escalation.

**8. UAC Bypass (User Account Control Bypass):**

* UAC is designed to limit the privileges of administrative users. However, there are many techniques to bypass UAC, such as exploiting auto-elevated programs that don’t trigger UAC prompts.
* Exploits that target UAC bypass typically involve abusing file paths or manipulating registry entries.

**9. Exploiting Vulnerabilities (Kernel Exploits):**

* Outdated or unpatched systems may be vulnerable to known exploits that target the Windows kernel, allowing an attacker to escalate privileges directly to SYSTEM.

**10. Password Mining:**

* Passwords for privileged accounts can sometimes be found in plain text within the system. Common places to check:
        1. Registry: Some software stores sensitive credentials in the registry.
        2. Configuration Files: Misconfigured applications might store sensitive information in easily accessible files.
        3. Cached Credentials: Cached credentials of privileged users can sometimes be extracted from memory or disk.

**11. SAM and SYSTEM Hive Extraction:**

* The Security Accounts Manager (SAM) database stores user passwords in a hashed format. If an attacker has access to the SYSTEM and SAM files, they can extract password hashes and crack them offline using tools like John the Ripper or hashcat.
* Tools like mimikatz or reg save can be used to extract these hives if the attacker has sufficient permissions.

**Tools for Privilege Escalation:**

* Windows Exploit Suggester: Scans the system to identify missing patches and potential vulnerabilities.
* mimikatz: A post-exploitation tool that can extract passwords, hashes, PINs, and Kerberos tickets from memory. It’s commonly used for token impersonation and dumping passwords.
* PowerUp: A PowerShell script that automates the search for common privilege escalation vectors on Windows.
* Sherlock: A PowerShell script that checks for known Windows privilege escalation vulnerabilities.




  
