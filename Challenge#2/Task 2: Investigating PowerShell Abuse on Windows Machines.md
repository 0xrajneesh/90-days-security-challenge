## Objective
The objective of this task is to detect, investigate, and respond to malicious or unauthorized PowerShell activity on a Windows machine using Sysmon. The project focuses on identifying suspicious PowerShell commands, encoded scripts, and activities like file downloads or execution from non-standard paths.

## Simulate the attack using Powershell command
```

Invoke-WebRequest -Uri "https://secure.eicar.org/eicar.com.txt" -OutFile "$env:USERPROFILE\Downloads\eicar.com.txt"

```