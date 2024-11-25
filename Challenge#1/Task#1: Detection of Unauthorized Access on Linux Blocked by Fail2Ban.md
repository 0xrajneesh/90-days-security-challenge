# Objective

Set up monitoring for unauthorized access attempts, trigger Fail2Ban to block malicious IPs, and analyze logs in Splunk.


## Install and Configure Fail2Ban

```
sudo apt update
sudo apt install fail2ban -y
```

## Edit the /etc/fail2ban/jail.local

```
[sshd]
enabled = true
port = ssh
logpath = /var/log/auth.log
maxretry = 3
bantime = 600
findtime = 600
```

## Restart Fail2Ban

```
/opt/splunkforwarder/bin/splunk add monitor /var/log/fail2ban.log
sudo systemctl restart fail2ban
```

## Content of /opt/splunkforwarder/etc/system/local/inputs.conf

```
[monitor:///var/log/fail2ban.log]
sourcetype = fail2ban_logs
index = fail2ban_logs
```
