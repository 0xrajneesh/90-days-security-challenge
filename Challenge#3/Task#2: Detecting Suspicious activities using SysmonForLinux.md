## Objective
To detect and investigate suspicious system changes (such as potential rootkits or malware) on a target Ubuntu machine using ELK SIEM with Fleet (Elastic Agent), Sysmon for Linux, and the Sysmon for Linux Connector, visualize these changes in real-time, and create alerts for abnormal system behaviors.

- `Detailed level`: Low
- `Difficulty level`: Medium

## Install Sysmon for Linux

```
wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb -O packages-microsoft-prod.deb

sudo dpkg -i packages-microsoft-prod.deb

sudo apt-get update
sudo apt-get install sysmonforlinux
```

## Attack Simulation

- **Simulate Creation of a Suspicious Package Manager Configuration File:**
    - Create a new file in the `/etc/apt/apt.conf.d/` directory (or any subdirectory within it) to simulate the configuration file creation. For example:
    
    ```bash
    bash
    Copy code
    sudo touch /etc/apt/apt.conf.d/99-suspicious-config
    
    ```
    
    - This will generate a "creation" event which should match the query in the rule.
- **Rename an Existing Configuration File (Optional):**
    - You can also rename a configuration file to further simulate a suspicious activity:
    
    ```bash
    bash
    Copy code
    sudo mv /etc/apt/apt.conf.d/01autoremove /etc/apt/apt.conf.d/01autoremove-backup
    
    ```
    
    - This action generates a "rename" event, which also matches the rule.

 - Run a Curl command to a suspicious site

  ```
  curl -X GET "https://bazaar.abuse.ch" -v
  ```
   
## Detection and Investigation on Elastic

- Go to **Kibana > Discover** and use queries like:   
    
     ```
     process.name: sysmon
     ```
    
- You should see logs for file and process changes. Apply filters for suspicious file paths or unauthorized processes.

