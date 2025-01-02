### **Objective**

The objective of this task is to monitor and investigate unauthorized changes to critical files and directories on a Windows machine using **Sysmon** for detailed logging and **Splunk** for analysis. This includes detecting file creation, modification, and deletion in sensitive directories to identify potential security incidents.

---

### **Steps**
1. Setting Up the Windows Machine
2. Configuring Splunk Universal Forwarder
3. Simulating Unauthorized File Changes
4. Detection and Investigation
5. Incident Response

---

### **1. Setting Up the Windows Machine**

- Verify the sysmon
    ```cmd
    sysmon -c sysmonconfig.xml
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

- Confirm `outputs.conf` is configured for the Splunk Indexer:
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

### **3. Simulating Unauthorized File Changes**

- Create a New File:
    ```cmd
    echo MaliciousContent > C:\Windows\System32\malicious.exe
    ```

- Modify an Existing File:
    ```cmd
    echo AlteredContent >> C:\Windows\System32\config.sys
    ```

- Delete a Critical File:
    ```cmd
    del C:\Windows\System32\important.dll
    ```

---

### **4. Detection and Investigation**

- Search Sysmon Logs in Splunk:

    - **Detect File Creations**:
        ```spl
        index=sysmon_logs EventCode=11 TargetFilename="*System32*" OR TargetFilename="*Program Files*" | stats count by TargetFilename, Image, User
        ```

    - **Detect File Modifications**:
        ```spl
        index=sysmon_logs EventCode=15 TargetFilename="*System32*" | stats count by TargetFilename, Image, User
        ```

    - **Detect File Deletions**:
        ```spl
        index=sysmon_logs EventCode=23 TargetFilename="*System32*" | stats count by TargetFilename, Image, User
        ```

- Correlate Events with Process Creation:
    ```spl
    index=sysmon_logs (EventCode=1 OR EventCode=11 OR EventCode=23) | transaction ProcessId maxspan=1m
    ```

- Visualize File Activity in Splunk:
    - Bar chart of most modified files in critical directories.
    - Pie chart of users performing file changes.
    - Timeline of file activities for investigative insights.

---

### **5. Incident Response**

1. **Isolate the Machine**:
    ```powershell
    New-NetFirewallRule -DisplayName "Block All Traffic" -Direction Outbound -Action Block
    ```

2. **Restore Critical Files**:
    ```powershell
    Copy-Item -Path "C:\Backup\config.sys" -Destination "C:\Windows\System32\config.sys" -Force
    ```

3. **Terminate Malicious Processes**:
    ```powershell
    Stop-Process -Name malicious -Force
    ```

4. **Analyze Malicious Files**:
    - Investigate content of suspicious files:
        ```powershell
        Get-Content "C:\Windows\System32\malicious.exe"
        ```
    - Remove malicious files:
        ```powershell
        Remove-Item -Path "C:\Windows\System32\malicious.exe" -Force
        ```

5. **Set Up Alerts in Splunk**:
    ```spl
    index=sysmon_logs (EventCode=11 OR EventCode=15 OR EventCode=23) TargetFilename="*System32*" | where like(User, "%unauthorized_user%")
    ```
