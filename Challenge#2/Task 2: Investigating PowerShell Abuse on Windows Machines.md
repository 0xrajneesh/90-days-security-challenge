### **Objective**

The objective of this task is to detect, investigate, and respond to malicious or unauthorized PowerShell activity on a Windows machine using Sysmon and Splunk. The focus is on identifying suspicious PowerShell commands, encoded scripts, and activities such as file downloads or executions from non-standard paths.    

`Detail-Level` - Medium

---

### **Steps**
1. Setting up Windows Machine
2. Setting up Splunk 
3. Simulating the attack and Visualizing the alert
4. Incident Response

---

### **1. Setting up Windows Machine**

- Install and configure Sysmon:
  - Install Sysmon with a predefined configuration:
    ```powershell
    sysmon.exe -accepteula -i sysmonconfig.xml
    ```
  - Verify Sysmon installation:
    ```cmd
    sc query sysmon64
    ```

- Edit the `inputs.conf` file for Sysmon logs:
    ```plaintext
    [WinEventLog://Microsoft-Windows-Sysmon/Operational]
    disabled = 0
    index = sysmon_logs
    sourcetype = XmlWinEventLog:Sysmon
    renderXml = false
    ```

---

### **2. Setting up Splunk**

- Configure the Splunk Universal Forwarder:
  - Edit `inputs.conf` for PowerShell and Security logs:
    ```plaintext
    [WinEventLog://Microsoft-Windows-PowerShell/Operational]
    disabled = 0
    index = powershell_logs
    sourcetype = WinEventLog:PowerShell
    ```

- Configure outputs to send logs to the Splunk Indexer:
  - Verify the connection:
    ```cmd
    splunk list forward-server
    ```

- Install Sysmon App for Splunk from Splunkbase.

---

### **3. Simulating the Attack and Visualizing the Alert**

- Simulate a suspicious file download:
  ```powershell
  Invoke-WebRequest -Uri "https://secure.eicar.org/eicar.com.txt" -OutFile "$env:USERPROFILE\Downloads\eicar.com.txt"
  ```
-  Visualize the events on the Splunk dashboard:
  ```
  index="sysmon_logs" sourcetype="XmlWinEventLog:Sysmon"
  ```
- Example of detected events:
  
  - Event 1: Process creation with Invoke-WebRequest.
  - Event 2: File creation for eicar.com.txt.
 ### 4. Incident Response
  1. Terminate Malicious Processes:
  
  - Identify and kill malicious PowerShell processes:
   ```
  Stop-Process -Name powershell -Force
  ```
  2. Block Malicious IPs:
  
  - Block suspicious outbound connections:
  ```
  New-NetFirewallRule -DisplayName "Block Malicious IP" -Direction Outbound -Action Block -RemoteAddress <malicious-ip>
  ```
  3. Set Up Alerts for Future Incidents:
  
  - Create a Splunk alert for suspicious PowerShell commands:
  ```
  index=sysmon_logs EventCode=1 CommandLine="*Invoke-WebRequest*" OR CommandLine="*EncodedC
  ```
