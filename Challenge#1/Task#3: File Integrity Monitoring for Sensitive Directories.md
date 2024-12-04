## Objective
The objective of this task is to monitor and detect unauthorized modifications, deletions, or attribute changes in sensitive directories, such as /etc/, using Auditd for real-time monitoring and Splunk for centralized log analysis. By simulating potential threats, such as file tampering or deletion, students will gain hands-on experience in setting up file integrity monitoring, analyzing logs for forensic purposes, and implementing incident response strategies to secure critical system files.


## Create an Auditd rule under /etc/audit/rules.d/audit.rules file
```
-w /etc/ -p wa -k file_integrity

```

## Update the Inputs.conf file

```

[monitor:///var/log/audit/audit.log]
sourcetype = auditd
index = linux_file_integrity


```