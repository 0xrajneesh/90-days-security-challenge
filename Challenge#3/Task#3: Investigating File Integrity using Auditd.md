## Objective
To investigate file integrity and detect unauthorized changes on a target Ubuntu machine using Auditd and ELK SIEM with Fleet (Elastic Agent). This guide will walk you through setting up Auditd to monitor file integrity, sending logs to Elasticsearch via Fleet, visualizing file changes in Kibana, and setting up alerts for suspicious activities like unauthorized file modifications.

- `Detailed level`: Low
- `Difficulty level`: Medium


## Setting Up the Target Ubuntu Machine with Auditd
- Install and configure Auditd
- Configure Auditd for File Integrity Monitoring
- Restart the Auditd service to apply the changes
- Verify Auditd Logging

## Attack Simulation
- Modify some critical files or add some files


## Detection and Investigation on Elastic
- Go to Kibana > Discover, search for logs related to file modifications:
  ```
  file.path: "/etc/passwd" AND event.action: "write"
  ```


