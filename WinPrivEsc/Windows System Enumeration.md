Windows System Basic Enumeration

- System Information:

```
systeminfo | findstr /B /C:"OS Name" /C:"OS Version" /C:"System Type"


```
- Hostname
- 
```
hostname
```
- Patching

```
wmic qfe get Caption, Description, HotFixID, InstalledOn
```

- Disks

```
wmic logicaldisk get caption,description,providername
```

- User enumeration

```
whoami
whoami /priv
whoami /groups
```

- Users on the machine

```
net user 
net user <username>
```

- Groups on the machine

```
net localgroup
net localgroup <groupname>

```

- network information

```
ipconfig /all

```

- arp information

```
arp -a 
```

- routing information

```
route print
```
- ports 

```
netstat -ano
```

- Is Windows defender running?

```
sc query windefend
```

-  find services 

```
sc queryex type= service 
```

- list firewall settings

```
netsh advfirewall firewall dump
netsh firewall show state
netsh firewall show config
```

