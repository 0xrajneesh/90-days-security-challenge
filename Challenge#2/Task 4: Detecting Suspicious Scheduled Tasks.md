### **Objective**

The objective of this task is to detect, investigate, and respond to unauthorized or suspicious scheduled tasks on a Windows machine that might be used for persistence, execution of malicious scripts, or lateral movement. This task uses **Sysmon** to monitor scheduled tasks and **Splunk** to analyze and visualize the data.    

`Detail-Level` - Medium

---

### **Steps**
1. Setting Up the Windows Machine
2. Configuring Splunk Universal Forwarder
3. Simulating Malicious Scheduled Task Activity
4. Detection and Investigation
5. Incident Response

---

### **1. Setting Up the Windows Machine**

- Verify Sysmon Installation:
    ```cmd
    sc query sysmon64
    ```

---

### **2. Configuring Splunk Universal Forwarder**

- Verify Log Forwarding:
    ```plaintext
    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = 0
    index = sysmon_logs
    sourcetype = WinEventLog:Sysmon
    ```

- Restart the Universal Forwarder:
    ```cmd
    splunk restart
    ```

---

### **3. Simulating Malicious Scheduled Task Activity**

- Create a New Scheduled Task:
    ```cmd
    schtasks /create /tn "MaliciousTask" /tr "C:\malware.exe" /sc once /st 12:00
    ```

- Modify an Existing Scheduled Task:
    ```cmd
    schtasks /change /tn "MaliciousTask" /tr "C:\evil_script.ps1"
    ```

- Delete a Scheduled Task:
    ```cmd
    schtasks /delete /tn "MaliciousTask" /f
    ```

- Execute a Scheduled Task:
    ```cmd
    schtasks /run /tn "MaliciousTask"
    ```

---

### **4. Detection and Investigation**

- Search Sysmon Logs in Splunk:

    - **Detect Task Creation**:
        ```spl
        index=sysmon_logs EventCode=1 Image="*schtasks.exe" | stats count by Image, CommandLine, User
        ```

    - **Detect Task Modification**:
        ```spl
        index=sysmon_logs EventCode=1 CommandLine="*change*" Image="*schtasks.exe"
        ```

    - **Detect Task Execution**:
        ```spl
        index=sysmon_logs EventCode=1 CommandLine="*run*" Image="*schtasks.exe"
        ```

- Correlate Events:
    ```spl
    index=sysmon_logs EventCode=1 Image="*schtasks.exe" OR EventCode=3
    ```

- Visualize Activity in Splunk:
    - Timeline of scheduled task creations and executions.
    - Table of users creating or modifying scheduled tasks.
    - Heatmap of task activity across machines.

---

### **5. Incident Response**

1. **Isolate the Machine**:
    ```powershell
    New-NetFirewallRule -DisplayName "Block All Traffic" -Direction Outbound -Action Block
    ```

2. **Terminate Malicious Processes**:
    ```powershell
    Stop-Process -Name malware -Force
    ```

3. **Delete Suspicious Scheduled Tasks**:
    ```cmd
    schtasks /delete /tn "MaliciousTask" /f
    ```

4. **Investigate Dropped Files**:
    - Check for malicious executables:
        ```powershell
        Get-ChildItem -Path "C:\malware.exe"
        ```

    - Remove malicious files:
        ```powershell
        Remove-Item -Path "C:\malware.exe" -Force
        ```

5. **Set Up Alerts in Splunk**:
    ```spl
    index=sysmon_logs EventCode=1 Image="*schtasks.exe" CommandLine="*create*" | where like(CommandLine, "%malicious_script%")
    ```
