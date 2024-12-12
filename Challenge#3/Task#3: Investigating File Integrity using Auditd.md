Install Auditd on the Ubuntu target machine to monitor file integrity.
```
sudo apt update
sudo apt install auditd audispd-plugins

sudo systemctl enable auditd
sudo systemctl start auditd
```

