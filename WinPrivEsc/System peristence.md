Maintaining persistence after acheiving SYSTEM.

Assumption is the SYSTEM user can't access corporate proxy so can't use a HTTP beacon

# Via Service 


- Upload payload
- Create a new serice 

# Via WMI

https://github.com/Sw4mpf0x/PowerLurk 

* EventConsumer
    * The action to perform - Execute the payload
* EventFilter
    * Trigger, such as a process starting
* FilterToConsumerBinding
    * links the EventConsumer and EventFilter together
 
```
powershell-import PowerLurk.ps1
powershell Register-MaliciousWMIEvent - EventName <name> -PermanentCommand <payload> -Trigger <trigger> -ProcessName <process>
```
* To load a payload whenever notepad is started, the trigger would be ProcessStart and the ProcessName would be notepad.exe 
