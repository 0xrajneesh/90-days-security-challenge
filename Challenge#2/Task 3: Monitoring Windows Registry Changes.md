### **Objective**

The objective of this task is to detect, investigate, and respond to unauthorized or suspicious changes to the Windows Registry, focusing on monitoring registry modifications that indicate malware installation, persistence mechanisms, or security configuration tampering using Sysmon and Splunk.    

`Detail-Level` - Medium


---

### **Steps**
1. Verify Sysmon Configuration
2. Configure Splunk Universal Forwarder
3. Simulate Malicious Registry Changes
4. Detection and Investigation
5. Incident Response

---

### **1. Verify Sysmon Configuration**

- CVerify Sysmon Configuration
  

### **2. Configure Splunk Universal Forwarder**

- Verify `inputs.conf` includes Sysmon logs:
    ```plaintext
    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = 0
    index = sysmon_logs
    sourcetype = WinEventLog:Sysmon
    ```

- Configure `outputs.conf` to send logs to the Splunk Indexer:
    ```plaintext
    [tcpout]
    defaultGroup = default-autolb-group

    [tcpout:default-autolb-group]
    server = <indexer-ip>:9997
    ```

- Restart the Universal Forwarder:
    ```cmd
    splunk restart
    ```

---

### **3. Simulate Malicious Registry Changes**

- Add a fake persistence entry:
    ```powershell
    New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MalwareTest" -Value "C:\malwaretest.exe"
    ```

- Modify a critical security setting:
    ```powershell
    Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters" -Name "DisableIPSourceRouting" -Value 1
    ```

- Delete a registry key:
    ```powershell
    Remove-Item -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run\MalwareSimulation"
    ```

---

### **4. Detection and Investigation**

- Search for fake persistence entries in Splunk:
    ```spl
    index=sysmon_logs EventCode=13 | where like(TargetObject, "%Run%")
    ```

- Detect registry key deletions:
    ```spl
    index=sysmon_logs EventCode=12 | stats count by TargetObject, Image, User
    ```

- Correlate registry events with process creation:
    ```spl
    index=sysmon_logs EventCode=1 Image="*powershell.exe"
    ```

- Visualize registry activities in dashboards:
    - Bar chart of registry paths most frequently modified.
    - Table of users performing registry changes.
    - Timeline of registry changes for investigative insights.

---

### **5. Incident Response**

1. **Isolate the Machine**:
    ```powershell
    New-NetFirewallRule -DisplayName "Block All Traffic" -Direction Outbound -Action Block
    ```

2. **Revert Registry Changes**:
    ```powershell
    Remove-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "MalwareSimulation"
    ```

3. **Investigate Files Referenced in Registry Entries**:
    ```powershell
    Get-ChildItem -Path "C:\malwaretest.exe"
    ```

4. **Terminate Malicious Processes**:
    ```powershell
    Stop-Process -Name powershell -Force
    ```

5. **Set Up Alerts for Future Incidents**:
    ```spl
    index=sysmon_logs EventCode=13 | where like(TargetObject, "%Run%") OR like(TargetObject, "%Startup%")
    ```
