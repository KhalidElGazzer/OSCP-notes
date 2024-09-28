In Linux, capabilities are a fine-grained privilege model introduced to split up the traditionally all-powerful root privileges into distinct units. This allows you to grant specific privileges to programs or processes without giving them full root access, enhancing security.

We can use the ```getcap``` tool to list enabled capabilities:
```
getcap -r / 2>/dev/null
```
GTFObins has a good list of binaries that can be leveraged for privilege escalation if we find any set capabilities.



















