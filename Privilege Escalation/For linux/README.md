
Privilege escalation is where a computer user uses system flaws or configuration errors to gain access to other user accounts in a computer system. By acquiring other accounts they get to access more files and they can also run administrator commands.
<br>
In privilege escalations, there are two types of privilege escalations:

1. Vertical privilege escalation.

2. Horizontal privilege escalation.

**Vertical privilege escalation**
---
Is where an attacker tries to access accounts with more permissions than the account they have. Most often attackers try to access accounts with administrator Privileges.<br>

**Horizontal privilege escalation** 
---
Is where an attacker has access rights to another user who has the same level of access he or she has.

In Linux, one can do privilege escalation(privesc) by:

1. Kernel exploits flaws.

2. Programs that the user can sudo.

3. Programs with setuid bit on.

4. Limited capabilities.

5. Changing the cron jobs file.

6. Writable folders.
   
8. Exploiting Network File Sharing(NFS) .


Why is it important?

It's rare when performing a real-world penetration test to be able to gain a foothold (initial access) that gives you direct administrative access. Privilege escalation is crucial because it lets you gain system administrator levels of access, which allows you to perform actions such as:

    Resetting passwords
    Bypassing access controls to compromise protected data
    Editing software configurations
    Enabling persistence
    Changing the privilege of existing (or new) users
    Execute any administrative command
