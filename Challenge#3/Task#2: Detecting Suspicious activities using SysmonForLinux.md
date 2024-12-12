## Objective
To detect and investigate suspicious system changes (such as potential rootkits or malware) on a target Ubuntu machine using ELK SIEM with Fleet (Elastic Agent), Sysmon for Linux, and the Sysmon for Linux Connector, visualize these changes in real-time, and create alerts for abnormal system behaviors.


## Install Sysmon for Linux

```
wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb -O packages-microsoft-prod.deb

sudo dpkg -i packages-microsoft-prod.deb

sudo apt-get update
sudo apt-get install sysmonforlinux
```

## Attack Simulation

```
sudo touch /etc/malicious_file

```
