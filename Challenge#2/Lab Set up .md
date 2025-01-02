# Splunk Setup for Windows

## **Phase 1: Splunk Enterprise Installation**

1. **Install Splunk Enterprise on a Local Machine or Server**:
   - Install Splunk Enterprise on a Windows machine or server.
   - Download Splunk Enterprise from [Splunk Downloads](https://www.splunk.com/en_us/download.html).
   - Follow the installation wizard to complete the setup.
   - Verify Splunk is running and accessible via the web interface:
     - Navigate to `http://<Splunk_Server_IP>:8000` to access the web UI.

2. **Configure Splunk Indexes**:
   - Create specific indexes for monitoring Windows logs and security events:
     1. Navigate to Splunk Dashboard: **Settings > Indexes > Add New Index**.
     2. Create the following indexes:
        - `windows_system_logs`
        - `security_events`
     3. Save the changes and restart the Splunk Server via **Settings > Server Control**.

---

## **Phase 2: Install and Configure Splunk Universal Forwarder on Windows**

1. **Download and Install the Splunk Universal Forwarder**:
   - Download the Splunk Universal Forwarder for Windows from [Splunk Downloads](https://www.splunk.com/en_us/download/universal-forwarder.html).
   - Run the installer and follow these steps:
     - Accept the license agreement.
     - Specify the installation directory.
     - Set the admin username and password.

2. **Configure the Forwarder to Communicate with Splunk Enterprise**:
   - Open the command prompt with administrator privileges.
   - Run the following commands to configure the forwarder:
     ```cmd
     cd "C:\Program Files\SplunkUniversalForwarder\bin"
     splunk enable boot-start
     splunk start
     splunk add forward-server <Splunk_Server_IP>:9997 -auth admin:Admin@123
     ```

3. **Add Monitors for Windows Logs**:
   - Use Splunk forwarder commands to monitor key Windows event logs:
     ```cmd
     splunk add monitor "C:\Windows\System32\winevt\Logs\Security.evtx"
     splunk add monitor "C:\Windows\System32\winevt\Logs\Application.evtx"
     splunk add monitor "C:\Windows\System32\winevt\Logs\System.evtx"
     splunk restart
     ```

4. **Configure Universal Forwarder Inputs**:
   - Create or edit the `inputs.conf` file:
     - Path: `C:\Program Files\SplunkUniversalForwarder\etc\system\local\inputs.conf`.
     - Example configuration:
       ```plaintext
       [WinEventLog://Security]
       disabled = false
       index = security_events
       sourcetype = WinEventLog:Security

       [WinEventLog://Application]
       disabled = false
       index = windows_system_logs
       sourcetype = WinEventLog:Application

       [WinEventLog://System]
       disabled = false
       index = windows_system_logs
       sourcetype = WinEventLog:System
       ```
   - Restart the Universal Forwarder to apply the changes:
     ```cmd
     splunk restart
     ```

5. **Enable and Verify Forwarding on Splunk Enterprise**:
   - On the Splunk Web UI, go to **Settings > Forwarding and Receiving > Configure Receiving**.
   - Add port `9997` as a receiving port.
   - Verify data flow by searching in Splunk:
     ```plaintext
     index="*" | stats count by sourcetype
     ```

---

## **Phase 3: Install and Configure Splunk Apps for Windows**

1. **Install Relevant Splunk Apps**:
   - **Splunk App for Windows Infrastructure**:
     - Download from [Splunkbase](https://splunkbase.splunk.com/).
     - Provides insights into Windows system performance, security, and logs.
     - Data sources: Windows Event Logs (`Security`, `Application`, `System`).
   - **Splunk Common Information Model (CIM)**:
     - Ensures normalized and correlated data across different sources.

2. **Visualize Data**:
   - Use dashboards in the Splunk App for Windows Infrastructure to monitor:
     - User login attempts and failures.
     - System and application performance.
     - Security-related events like unauthorized access or policy violations.

---

## **Phase 4: Verify Data in Splunk**

- Run specific search queries to verify data ingestion:
  - **For Windows system logs**:
    ```plaintext
    index=windows_system_logs | stats count by sourcetype
    ```
  - **For security events**:
    ```plaintext
    index=security_events | stats count by sourcetype
    ```
