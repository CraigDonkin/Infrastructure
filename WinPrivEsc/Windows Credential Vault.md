# Windows Credential Vault

- Windows Vault stroes credentials so that Windows can log users in automatically. 

- cmdkey is a built in tool that can cache domain user credentials 
- https://docs.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754243(v=ws.11)?redirectedfrom=MSDN

- List cached creds

```
cmdkey /list
```

- Can then use the /savecred option with run as to use these credentials

```
runas.exe /user:Domain\username /savecred "C:\windows\system32\cmd.exe"
```

