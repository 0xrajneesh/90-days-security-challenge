## Objective
The objective of this task is to detect, investigate, and respond to unauthorized or suspicious scheduled tasks on a Windows machine that might be used for persistence, execution of malicious scripts, or lateral movement. This task uses Sysmon to monitor scheduled tasks and Splunk to analyze and visualize the data.

## Simulate Malicious Scheduled Task Activity

```
schtasks /create /tn "MaliciousTask" /tr "C:\malware.exe" /sc once /st 12:00

```