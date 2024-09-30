A Dynamic Link Library (DLL) is a file containing code and data that multiple programs can use simultaneously. DLLs promote modular code, efficient memory usage, and faster execution times.
How DLLs Work

* When programs need a DLL, they may not specify its full path (e.g., using just `important.dll` instead of` C:\Windows\System32\important.dll`).
* This omission leads the system to follow a predefined search order to locate the DLL, starting with the application's directory.
* If the DLL is not found in the application directory, the system continues searching through various directories listed in the system's PATH.

It should be noted that when an application needs to load a DLL it will go through the following order:

* The directory from which the application is loaded
* C:\Windows\System32
* C:\Windows\System
* C:\Windows
* The current working directory
* Directories in the system PATH environment variable
* Directories in the user PATH environment variable

الخلاصه
---
[dll_hijacking.pdf](https://github.com/user-attachments/files/17182411/dll_hijacking.pdf)






    
