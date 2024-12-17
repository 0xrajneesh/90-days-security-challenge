
### Splunk Set up

1. **Install Splunk Enterprise on a Local Machine or Server**:
    - Install Splunk Enterprise on a VM or server (Linux or Windows) or use Splunk Cloud for the project.
    - Ensure Splunk is running and accessible via the web interface.
    - Navigate to `http://<Splunk_Server_IP>:8000` to access the web UI.
2. **Configure Splunk Indexes**:
    - Create specific indexes for monitoring Ubuntu logs and security events.
    - Go to Splunk Dashboard, under setting> indexes and click on Add Index
    - Add `linux_os_logs`
    - Add `security_incidents`
    - Save
    - Restart the Splunk Server from Server Control

### **Phase 2: Install and Configure Ubuntu Universal Forwarder**

1. **Download Splunk Universal Forwarder on Ubuntu**:
    - On the Ubuntu machine, download and install the Splunk Universal Forwarder:
        
        ```bash
        
        wget -O splunkforwarder-9.3.1-0b8d769cb912-linux-2.6-amd64.deb "https://download.splunk.com/products/universalforwarder/releases/9.3.1/linux/splunkforwarder-9.3.1-0b8d769cb912-linux-2.6-amd64.deb"
        
        chmod +x  splunkforwarder-9.3.1-0b8d769cb912-linux-2.6-amd64.deb
        dpkg -i splunkforwarder-<version>-linux-2.6-amd64.deb
        
        ```
        
2. **Configure Universal Forwarder**:
    - Set up the forwarder to communicate with Splunk Enterprise:
        
        ```bash
        
        /opt/splunkforwarder/bin/splunk enable boot-start
        
        # Next, set up admin username and password
         
        /opt/splunkforwarder/bin/splunk start
        /opt/splunkforwarder/bin/splunk add forward-server 65.20.70.5:9997 -auth admin:Admin@123
        
        ```
        
3. **Add Monitors for Ubuntu Logs**:
    - Monitor important system logs such as auth, syslog, and audit logs:
        
        ```bash
        
        /opt/splunkforwarder/bin/splunk add monitor /var/log/syslog
        	/opt/splunkforwarder/bin/splunk add monitor /var/log/auth.log
        
        # Next, restart the UF
        /opt/splunkforwarder/bin/splunk restart
        
        ```
        
4. **Enable and Verify the Forwarding on Splunk** :
    - Login to Splunk, go to setting tab and click on forwarding and receiving. Under the receive data, Add 9997 port.
    - Ensure the forwarder is working and data is being sent to Splunk by checking:
        - Splunk Web UI: **Settings > Forwarding and Receiving > Forwarded Data**.

1. Verify the events on Splunk:
    - go to Splunk search and reporting
    - Run the search query `index=”*”`

1. Configure Universal Forwarder Inputs
    
    Step 1: Create the `inputs.conf` File
    
    You can create the `inputs.conf` file in the `/opt/splunkforwarder/etc/system/local/` directory by using the following command:
    
    ```bash
    
    sudo touch /opt/splunkforwarder/etc/system/local/inputs.conf
    
    ```
    
    Step 2: Edit the `inputs.conf` File
    
    Now, open the newly created `inputs.conf` file and configure it to monitor the logs you want to forward to Splunk.
    
    ```bash
    sudo nano /opt/splunkforwarder/etc/system/local/inputs.conf
    
     
    ****
    ```
    
    Example Configuration for `inputs.conf`
    
    **To monitor Ubuntu system logs**:
    
    ```
    
    [monitor:///var/log/syslog]
    disabled = false
    index = linux_os_logs
    sourcetype = syslog
    
    ```
    
    **To monitor security events** (e.g., failed login attempts) in a separate index `security_incidents`:
    
    ```
    
    [monitor:///var/log/auth.log]
    disabled = false
    index = security_incidents
    sourcetype = linux_secure
    whitelist = Failed|invalid|Denied
    
    ```
    
    - **Explanation**:
        - `monitor:///var/log/syslog`: Monitors system log files.
        - `monitor:///var/log/auth.log`: Monitors authentication logs (for example, SSH login attempts).
        - `monitor:///var/log/audit/audit.log`: Monitors audit logs.
        - `index = linux_os_logs`: Sends the logs to the `linux_os_logs` index.
        - `index = security_incidents`: Filters and forwards failed login attempts to the `security_incidents` index.
    
    Step 3: Restart the Splunk Universal Forwarder
    
    Once you've configured the `inputs.conf` file, restart the Universal Forwarder to apply the changes:
    
    ```bash
    sudo /opt/splunkforwarder/bin/splunk restart
    ```
    
    Step 4: Verify Data in Splunk
    
    Now, verify that the logs are flowing into Splunk by searching in the appropriate indexes. You can do this by running the following queries in Splunk's Search & Reporting app:
    
    - **For general logs (stored in `linux_os_logs`)**:
        
        ```bash
        index=linux_os_logs | stats count by sourcetype
        ```
        
    - **For security incidents (stored in `security_incidents`)**:
        
        ```bash
        index=security_incidents | stats count by sourcetype
        ```
        

### **Phase 3: Install and Configure Splunk Apps**

1. **Install Relevant Apps**:
    - **Splunk App for Unix and Linux**: Provides insights into Linux/Unix system performance, security, and configuration. Download it from Splunkbase and install it on your Splunk instance.
        - **Data sources**: `/var/log/syslog`, `/var/log/auth.log`
        - Use this app to visualize Ubuntu machine performance, login attempts, and user activities.
    - **Splunk Common Information Model (CIM)**: Ensures that events from different sources are normalized and mapped to the common schema, improving correlation across security events.
