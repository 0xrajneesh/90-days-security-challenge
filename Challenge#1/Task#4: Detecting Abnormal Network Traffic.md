## Install Suricata
```
sudo add-apt-repository ppa:oisf/suricata-stable
sudo apt-get update
sudo apt-get install suricata -y
```

## Installing Emerging Threat ruleset
```
cd /tmp/ && curl -LO https://rules.emergingthreats.net/open/suricata-6.0.8/emerging.rules.tar.gz
sudo tar -xvzf emerging.rules.tar.gz && sudo mkdir /etc/suricata/rules && sudo mv rules/*.rules /etc/suricata/rules/
sudo chmod 640 /etc/suricata/rules/*.rules
```

## Configure Suricata 
```
HOME_NET: "<UBUNTU_IP>"
EXTERNAL_NET: "any"

default-rule-path: /etc/suricata/rules
rule-files:
- "*.rules"

# Linux high speed capture support
af-packet:
  - interface: enp0s3
```

## Configure inputs.conf file in Splunk UF
```
[monitor:///var/log/suricata/fast.log]
sourcetype = suricata
index = network_security_logs
```
