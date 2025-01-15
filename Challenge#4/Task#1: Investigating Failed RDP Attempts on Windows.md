### **Objective:**

To investigate **failed RDP login attempts** on a **Windows** target machine using **ELK SIEM** with Fleet (Elastic Agent), analyze patterns of failed login attempts, and detect potential brute-force attacks or unauthorized access attempts.


## Attack Simulation
- To simulate a brute-force attack or multiple failed RDP login attempts, use a tool like **Hydra** or **Medusa**:
    
    ```bash
    bash
    Copy code
    hydra -l <username> -P /path/to/passwordlist.txt rdp://<target-ip>
    
    ```
    
- Alternatively, manually attempt incorrect logins:
    
    ```bash
    bash
    Copy code
    ssh <username>@<target-ip>
    
    ```

  ## Detection and Investigation on Elastic

  - Go to **Kibana > Discover** and run a query to filter for **failed RDP login attempts**:
    
    ```
    winlog.channel: Microsoft-Windows-Sysmon/Operational and 
    event.code 3 and source.ip 139.84.165.142
    ```
    
- Review logs for **source IP**, **user account**, and **failure reason**.
