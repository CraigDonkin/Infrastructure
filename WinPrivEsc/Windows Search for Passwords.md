# Windows Search for Passwords

```
findstr /si password *.config *.ini *.xml *.txt

dir /s *pass* == *cred* == *vnc* == *config*

reg query HKLM /f password /t REG_SZ /s

reg query HKCU /f password /t REG_SZ /s
```

- find password in all files (could take long time)

```
findstr /spin "password" *.*
```

- Common files

```
sysprep.inf
unattend.xml
vnc.ini
ultravnc.ini

```