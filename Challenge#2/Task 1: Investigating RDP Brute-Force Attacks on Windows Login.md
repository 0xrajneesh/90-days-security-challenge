## Objective
The objective of this task is to detect, investigate, and respond to brute-force attacks targeting the Remote Desktop Protocol (RDP) service on a Windows machine. This task uses Sysmon and Splunk to monitor failed and successful login attempts and identify suspicious patterns indicative of an attack.

## Steps
1. Setting up Windows Machine
2. Setting up Splunk 
3. Simulate the attack and Visualize the alert
4. Incident Response

## Update the inputs.conf file to collect Sysmon logs
```
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = sysmon_logs
sourcetype = XmlWinEventLog:Sysmon
renderXml = false

```

## Simulate the RDP brute force attack
```

hydra -l administrator -P /path/to/passwords.txt rdp://<windows-machine-ip>


```
