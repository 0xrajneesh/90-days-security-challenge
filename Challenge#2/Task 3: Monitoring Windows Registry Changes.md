## Objective
The objective of this task is to detect, investigate, and respond to unauthorized or suspicious changes to the Windows Registry, focusing on monitoring registry modifications that indicate malware installation, persistence mechanisms, or security configuration tampering using Sysmon and Splunk.


## Simulate Malicious Registry changes
```
New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MalwareSimulation" -Value "C:\malware.exe"

``` 