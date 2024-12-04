## Objective
The objective of this task is to monitor and investigate unauthorized changes to critical files and directories on a Windows machine using Sysmon for detailed logging and Splunk for analysis. This includes detecting file creation, modification, and deletion in sensitive directories to identify potential security incidents.

## Simulate Unauthorized File Changes

```
echo MaliciousContent > C:\Windows\System32\malicious.exe

```