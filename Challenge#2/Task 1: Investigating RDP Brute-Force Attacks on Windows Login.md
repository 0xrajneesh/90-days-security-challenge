## Objective
The objective of this task is to detect, investigate, and respond to brute-force attacks targeting the Remote Desktop Protocol (RDP) service on a Windows machine. This task uses Sysmon and Splunk to monitor failed and successful login attempts and identify suspicious patterns indicative of an attack.    
`Detail-Level` - Medium

## Steps
1. Setting up Windows Machine
2. Setting up Splunk 
3. Simulate the attack and Visualize the alert
4. Incident Response

### 1. Setting up Windows Machine

- Edit the inputs.conf file
```
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = 0
index = sysmon_logs
sourcetype = XmlWinEventLog:Sysmon
renderXml = false

```

### 2. Setting up Splunk 
- Create a Sourcetype(if required)
- Install Sysmon App from Splunkbase

### 3. Simulate the attack and Visualize the alert
- Use manual login attempts or tools liek Hydra
```
hydra -l administrator -P /path/to/passwords.txt rdp://<windows-machine-ip>
```
- Visualize the event on Splunk Dashboard
```
index="sysmon_logs"  sourcetype = XmlWinEventLog:Sysmon
```

### 4. Incident Response
### **Incident Response**

1. **Block Malicious IPs**:
- Use the Windows Firewall to block suspicious IP addresses:
```
        powershell
        Copy code
        New-NetFirewallRule -DisplayName "Block RDP Brute Force" -Direction Inbound -Action Block -RemoteAddress 139.84.176.244
         
 ```
        
2. **Reset Compromised Accounts**:
    - Reset passwords for compromised accounts:
        
 ```
        net user <username> <new_password>
        
 ```
        
3. **Enable Account Lockout Policy**:
    - Set a lockout policy to prevent repeated brute-force attempts:
        
```
        secpol.msc
 ```
        
        - Navigate to `Account Lockout Policy` and configure:
            - Lockout Threshold: 5 failed attempts.
            - Lockout Duration: 15 minutes.
     
