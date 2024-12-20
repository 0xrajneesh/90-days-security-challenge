**Objective**:
Set up a network monitoring system to detect abnormal traffic patterns, including data exfiltration and command-and-control (C2) communication, by leveraging **Suricata** with **Emerging Threats (ET)** rules and analyzing logs using **Splunk**. Students will simulate a network-based attack and respond to alerts generated by the ET rules.   
- **Detailed level**: Full
- **Difficulty level**: Medium

### **Tools Used**

1. **Suricata**: For monitoring and detecting abnormal network traffic using ET rules.
2. **Emerging Threats Rules**: For detecting malware, C2 communication, and data exfiltration.
3. **Splunk Universal Forwarder**: For forwarding Suricata logs to Splunk.
4. **Splunk**: For log analysis and visualization.

---

### **Step-by-Step Guide**

---

### **Step 1: Prerequisites**

1. An **Ubuntu Server** with sudo privileges.
2. **Suricata** installed as an Intrusion Detection System (IDS).
3. **Emerging Threats (ET) rules** configured in Suricata.
4. **Splunk Universal Forwarder** installed and configured to forward logs.
5. A simulated attack that triggers Emerging Threats rules.

---

### **Step 2: Install and Configure Suricata**

1. **Install Suricata**:
    
    ```bash
    
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo apt-get install suricata -y
    
    ```
    
2. **Enable Emerging Threats Rules**:
    - Download ET rules using the **Oinkmaster** tool:
        
        ```bash
        
        cd /tmp/ && curl -LO https://rules.emergingthreats.net/open/suricata-6.0.8/emerging.rules.tar.gz
        sudo tar -xvzf emerging.rules.tar.gz && sudo mkdir /etc/suricata/rules && sudo mv rules/*.rules /etc/suricata/rules/
        sudo chmod 640 /etc/suricata/rules/*.rules
        
        ```
        
3. **Update Suricata Configuration**:
    - Open the Suricata configuration file:
        
        ```bash
        
        sudo nano /etc/suricata/suricata.yaml
        
        ```
        
    - Update the `rule-files` section to include ET rules:
        
        ```yaml
        
        rule-files:
          - emerging-web_client.rules
          - emerging-malware.rules
          - emerging-attack_response.rules
          - emerging-trojan.rules
        
        ```
        
    - Ensure that the `HOME_NET` variable is set to your internal network range:
        
        ```yaml
        HOME_NET: "<UBUNTU_IP>"
        EXTERNAL_NET: "any"
        
        default-rule-path: /etc/suricata/rules
        rule-files:
        - "*.rules"
        
        # Global stats configuration
        stats:
        enabled: yes
        
        # Linux high speed capture support
        af-packet:
          - interface: enp0s3
        
        ```
        
4. **Restart and Test Suricata Configuration**:
    
    ```bash
    
    sudo systemctl restart suricata
    
    ```
    
5. **Start Suricata**:
    
    ```bash
    bash
    Copy code
    sudo systemctl start suricata
    sudo systemctl enable suricata
    
    ```
    
6. **Verify Suricata Status**:
    
    ```bash
    bash
    Copy code
    sudo systemctl status suricata
    
    ```
    

---

### **Step 3: Simulate Network-Based Attacks**

1. **On the Attacker Machine**:
    - Set up a listener to receive a specific file:
        
        ```bash
        nmap -sS 65.20.67.137
        
        ```
        

---

### **Step 4: Install and Configure Splunk Universal Forwarder**

1. **Install Splunk Universal Forwarder**:
    - Download and install the Splunk Universal Forwarder:
2. **Configure Splunk to Monitor Suricata Logs**:
    - Edit the Splunk inputs configuration file:
        
        ```bash
        
        sudo nano /opt/splunkforwarder/etc/system/local/inputs.conf
        
        ```
        
    - Add the following entry to monitor Suricata logs:
        
        ```
        
        [monitor:///var/log/suricata/eve.json]
        sourcetype = suricata
        index = network_security_logs
        
        ```
        
    - Save and restart the Splunk Universal Forwarder:
        
        ```bash
        
        sudo /opt/splunkforwarder/bin/splunk restart
        
        ```
        
3. **Verify Log Forwarding**:
    - Use this query in Splunk to check for Suricata alerts:
        
        ```
        
        index=network_security_logs sourcetype=suricata
        
        ```
        

---

### **Step 5: Analyze Logs in Splunk**

1. **Search for Alerts Triggered by ET Rules**:
    - Use the following query to find alerts generated by Emerging Threats rules:
        
        ```
        
        index=network_logs sourcetype=suricata alert="ET*" src_ip="<attacker-ip>"
        
        ```
        
2. **Investigate Traffic Patterns**:
    - Query for specific traffic types or destinations:
        
        ```
        spl
        Copy code
        index=network_logs sourcetype=suricata dest_ip="<attacker-ip>"
        
        ```
        
3. **Visualize Alerts**:
    - Create dashboards to monitor:
        - Top triggered ET rules.
        - Source and destination IPs involved in alerts.
        - Time trends of abnormal traffic.

---

### **Step 6: Incident Response**

1. **Block Malicious IPs**:
    - Add the attacker’s IP to the firewall blocklist:
        
        ```bash
        bash
        Copy code
        sudo ufw deny from <attacker-ip>
        
        ```
        
2. **Terminate Active Connections**:
    - Identify and terminate malicious connections:
        
        ```bash
        bash
        Copy code
        sudo netstat -tulpn | grep <attacker-ip>
        sudo kill <pid>
        
        ```
   
