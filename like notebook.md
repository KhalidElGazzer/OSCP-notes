AutoLogon creds
-
During our privilege escalation proccess, if we got this creds using ```winPEAS.exe``` binary we could use them to RDP over the target machine.
![Screenshot from 2024-10-06 00-51-57](https://github.com/user-attachments/assets/9baed7c0-548c-4a51-9a5e-149b51864e14)

```
xfreerdp /u:administrator /p:<password> /v:<target ip>
```







