**Objective**: Set up monitoring for unauthorized access attempts, trigger Fail2Ban to block malicious IPs, and analyze logs in Splunk.

---

### **Step-by-Step Instructions**

### **Step 1: Prerequisites**

1. Ensure you have an **Ubuntu Server** ready for this task.
2. Install the necessary tools:
    - Fail2Ban for SSH protection.
    - Splunk Universal Forwarder for log forwarding.

---

### **Step 2: Install and Configure Fail2Ban**

1. **Install Fail2Ban**:
    
    ```bash
    
    sudo apt update
    sudo apt install fail2ban -y
    
    ```
    
2. **Configure Fail2Ban for SSH**:
    - Open the Fail2Ban configuration file:
        
        ```bash
        
        sudo nano /etc/fail2ban/jail.local
        
        ```
        
    - Add the following lines to protect the SSH service:
        
        ```
        [sshd]
        enabled = true
        port = ssh
        logpath = /var/log/auth.log
        maxretry = 3
        bantime = 600
        findtime = 600
        
        ```
        
    - Save and close the file.
3. **Add Restart Fail2Ban** to apply changes:
    
    ```bash
    /opt/splunkforwarder/bin/splunk add monitor /var/log/fail2ban.log
    sudo systemctl restart fail2ban
    
    ```
    
4. **Verify Fail2Ban Status**:
    
    ```bash
    
    sudo fail2ban-client status
    
    ```
    
    Look for the `sshd` jail in the output.
    
5. Create index and sourcetype in Splunk platform
    
    ```css
    Create index and sourcetype and name them as fail2ban_logs
    ```
    

---

### **Step 3: Simulate Unauthorized SSH Access**

1. 
    
    ```bash
    bash
    Copy code
    ssh user@<target-ip>
    
    ```
    

### **Step 3: Simulate a Brute-Force Attack with Hydra**

1. **Prepare a wordlist** with common passwords:
    - Use a pre-existing wordlist (e.g., `/usr/share/wordlists/rockyou.txt`) or create a custom one:
        
        ```bash
        
        echo -e "password123\nadmin123\n12345678\ntestpass" > passwords.txt
        
        ```
        
2. **Run Hydra** to perform the brute-force attack:
    
    ```bash
    
    hydra -l testuser -P passwords.txt <target-ip> ssh
    
    ```
    
    - Replace `<target-ip>` with the IP address of the Ubuntu server.
3. **Observe Fail2Ban Action**:
    - As the attack progresses, Fail2Ban will detect multiple failed login attempts and block the attacking IP.
    - Check Fail2Ban logs on the server:
        
        ```bash
        
        tail -f /var/log/fail2ban.log
        
        ```
        
4. **Monitor Fail2Ban Logs**:
    
    ```bash
    
    tail -f /var/log/fail2ban.log
    
    ```
    
    - Check for entries indicating that Fail2Ban has banned the attacking IP.

---

### **Step 4: Configure Splunk for Log Monitoring**

1. Make sure your Universal forwarder is up and running on Ubuntu server.
2. **Set up log forwarding for Fail2Ban**:
    - Create an input configuration:
        
        ```bash
        
        sudo nano /opt/splunkforwarder/etc/system/local/inputs.conf
        
        ```
        
    - Add the following configuration to monitor Fail2Ban logs:
        
        ```
        
        [monitor:///var/log/fail2ban.log]
        sourcetype = fail2ban_logs
        index = fail2ban_logs
        
        ```
        
    - Save and restart the Splunk Universal Forwarder:
        
        ```bash
        
        sudo /opt/splunkforwarder/bin/splunk restart
        
        ```
        

---

### **Step 5: Analyze Logs in Splunk**

1. **Log in to Splunk** and navigate to the Search and Reporting App.
2. Use the following SPL query to identify blocked IPs:
    
    ```
    
    index=linux_logs sourcetype=fail2ban action="ban"
    
    ```
    
3. **Visualize Results**:
    - Create a dashboard to monitor trends of failed login attempts and banned IPs.

---

### **Step 6: Validate Incident Response**

1. **Validate Banned IPs**:
    
    ```bash
    
    sudo fail2ban-client status sshd
    
    ```
    
    Verify the attacking IP is listed as banned.
    
2. **Unban IPs (if needed)**:
    
    ```bash
    
    sudo fail2ban-client set sshd unbanip <banned-ip>
    
    ```
    
    - Reporting: **Post-Incident Reporting**
        
        ### **Incident Report: Detection of Unauthorized Access Blocked by Fail2Ban**
        
        ---
        
        ### **1. Incident Overview**
        
        **Incident Title**: Unauthorized SSH Access Attempts Blocked by Fail2Ban
        
        **Date and Time**: [Insert Date & Time of Incident]
        
        **Reported By**: [Your Name/Role]
        
        **Server Affected**: Ubuntu Server [Insert Server IP or Hostname]
        
        ---
        
        ### **2. Incident Description**
        
        On [insert date], a brute-force attack was detected targeting the SSH service on the Ubuntu server. The attacker attempted multiple failed SSH login attempts to the `testuser` account using the Hydra tool. Fail2Ban identified the unauthorized activity and automatically blocked the attacker’s IP address.
        
        ---
        
        ### **3. Key Events Timeline**
        
        | **Time (UTC)** | **Event** |
        | --- | --- |
        | [Insert Time] | Multiple failed SSH login attempts detected. |
        | [Insert Time] | Fail2Ban triggered and banned the attacker’s IP address. |
        | [Insert Time] | Logs forwarded to Splunk for analysis. |
        
        ---
        
        ### **4. Affected Systems and Services**
        
        - **System**: Ubuntu Server ([Insert Hostname/IP])
        - **Service**: SSH (port 22)
        
        ---
        
        ### **5. Incident Investigation**
        
        ### **Attack Method**:
        
        The attacker used Hydra to perform a brute-force attack, repeatedly attempting to guess the `testuser` account’s password.
        
        ### **Log Analysis**:
        
        - **Fail2Ban Logs**:
        Example log entry:
            
            ```less
            less
            Copy code
            2024-11-17 10:15:43,123 fail2ban.actions [12345]: NOTICE [sshd] Ban 192.168.1.100
            
            ```
            
        - **Splunk Query**:
            
            ```
            spl
            Copy code
            index=linux_logs sourcetype=fail2ban action="ban"
            
            ```
            
            - Results showed multiple failed login attempts originating from IP `192.168.1.100`.
        
        ---
        
        ### **6. Incident Response**
        
        ### **Actions Taken**:
        
        1. **Automated Actions**:
            - Fail2Ban identified the repeated failed logins and banned the attacker’s IP (`192.168.1.100`) for 600 seconds.
        2. **Manual Actions**:
            - Verified the banned IP and confirmed malicious activity.
            - Cleared the banned IP after investigation:
                
                ```bash
                bash
                Copy code
                sudo fail2ban-client set sshd unbanip 192.168.1.100
                
                ```
                
        
        ---
        
        ### **7. Root Cause Analysis**
        
        The attack exploited the SSH service by attempting to brute-force login credentials. The server's defenses (Fail2Ban) successfully mitigated the attack.
        
        ---
        
        ### **8. Recommendations for Mitigation**
        
        1. **Enforce Strong Authentication**:
            - Use SSH keys instead of password-based authentication.
            - Disable password authentication in the SSH configuration:Set:
                
                ```bash
                sudo nano /etc/ssh/sshd_config
                
                ```
                
                ```perl
                PasswordAuthentication no
                
                ```
                
        2. **Restrict SSH Access**:
            - Allow only trusted IP addresses in `/etc/hosts.allow` or configure a firewall rule.
        3. **Increase Fail2Ban Sensitivity**:
            - Reduce `maxretry` to block IPs more quickly.
        4. **Monitor Logs Regularly**:
            - Create Splunk alerts to notify security teams of suspicious activity:
                
                ```
                
                index=linux_logs sourcetype=fail2ban action="ban"
                
                ```
                
        
        ---
        
        ### **9. Lessons Learned**
        
        1. **Proactive Measures**: Fail2Ban is effective in mitigating brute-force attacks when configured properly.
        2. **Need for Continuous Monitoring**: Real-time log analysis via Splunk is essential for early detection.
        3. **Future Hardening**: Additional layers of security, such as multi-factor authentication (MFA), should be implemented.
        
        ---
        
        ### **10. Conclusion**
        
        The attempted brute-force attack was successfully mitigated by Fail2Ban. The logs forwarded to Splunk allowed a detailed analysis of the incident, confirming no unauthorized access was gained. Mitigation measures have been implemented to prevent similar incidents in the future.
        
        ---
        
        **Prepared By**: [Your Name]
        
        **Role**: [Your Role, e.g., Cybersecurity Analyst]
        
        **Date**: [Insert Date]
