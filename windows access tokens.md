Tokens in Windows are like digital ID cards for users or processes.

They hold information about who the user is, what permissions they have, and what groups they belong to.

Types of Tokens:
-

* **Primary Token**:


1. This is the main token created when a user logs in.
2. It represents the user's identity and permissions.
3. Whenever a user tries to do something (like open a file or access a network resource), Windows looks at this token to see if they have the right to do that action.

* **Impersonation Token**:

1. This allows a process (like a program) to act on behalf of another user.
2. It gives the program the ability to use the permissions of that user temporarily.

**How Permission Checks Work**
-

When a Process Acts: 

* Imagine you’re using a program (like a text editor) that wants to open a protected file.
* Before allowing this action, Windows checks the program’s Primary Token.

Determining Permissions:
--

* Windows looks at the Primary Token to see if it has the right to open that file.
* If the token does not have the necessary permissions (like if it’s a standard user trying to access a file meant for administrators), Windows will deny the action.

**Why Does This Matter?**
--

* Even if a process has impersonated a higher privilege user (like an administrator), Windows will still check against the process's Primary Token for permission.
* If the process's Primary Token has limited permissions, the impersonation doesn't matter for that action.

Example Scenario

1. You Log In: When you log in to your computer as a standard user, your account's Primary Token is created. This token allows you to perform actions like opening files and running programs within your allowed permissions.

2. Using a Program: Now, suppose you have a program that can impersonate the administrator user. When this program tries to access a restricted file:
3. Windows checks the program's Primary Token, which still reflects your standard user status.
4 .Because your Primary Token does not have the right to access the file, Windows denies the action, even though the program is pretending to be an administrator.

    >Changing the Token: To do what you want (like access the restricted file), you need to migrate to a process that is already running as an administrator.
        This changes the Primary Token of your program to that of the administrator, allowing it to perform the desired actions.

For an impersonation token, there are different levels:
-

    SecurityAnonymous: current user/client cannot impersonate another user/client
    SecurityIdentification: current user/client can get the identity and privileges of a client but cannot impersonate the client
    SecurityImpersonation: current user/client can impersonate the client's security context on the local system
    SecurityDelegation: current user/client can impersonate the client's security context on a remote system

Commonly Abused Privileges:
-

Privileges are permissions assigned to user accounts, allowing them to perform specific actions. The following are some commonly abused privileges:

    SeImpersonatePrivilege: Allows a user to impersonate another user.
    SeAssignPrimaryPrivilege: Allows a user to assign primary tokens to other users.
    SeTcbPrivilege: Allows a user to act as part of the operating system.
    SeBackupPrivilege: Allows a user to bypass file security to back up files.
    SeRestorePrivilege: Allows a user to bypass file security to restore files.
    SeCreateTokenPrivilege: Allows a user to create new access tokens.
    SeLoadDriverPrivilege: Allows a user to load and unload device drivers.
    SeTakeOwnershipPrivilege: Allows a user to take ownership of files or other objects.
    SeDebugPrivilege: Allows a user to debug processes, which can lead to unauthorized access or privilege escalation.

If we do this command we will list the available privileges:
```
whoami /priv
```


![Screenshot from 2024-10-05 06-19-35](https://github.com/user-attachments/assets/68349056-ad1b-4ff7-bd7b-962b131c5ff1)
You can see that two privileges(SeDebugPrivilege, SeImpersonatePrivilege) are enabled. Let's use the incognito module that will allow us to exploit this vulnerability. 

**Incognito module**
-
The Incognito module in Metasploit is used to manipulate access tokens, allowing an attacker to impersonate other users on a Windows system. This can be particularly useful for privilege escalation and testing security controls.

In our meterpreter session we could load it:
```
load incognito
```
To see which tokens are available for impersonation, use:
```
list_tokens -g
```
This command will display a list of tokens you can impersonate, typically including ```built-in administrative``` accounts.

Impersonate a Token: To impersonate a token, use the command:
```
impersonate_token <token_name>

impersonate_token "BUILTIN\Administrators"
```
Finally, we got the ```NT AUTHORITY\SYSTEM```

>To ensure that you have the necessary permissions, it’s often a good practice to migrate to a process that has higher privileges


