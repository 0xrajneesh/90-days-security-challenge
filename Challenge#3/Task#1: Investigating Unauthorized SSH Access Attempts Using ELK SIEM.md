## Objective
To investigate unauthorized SSH access attempts on a target Linux machine using ELK SIEM with Fleet (Elastic Agent), visualize suspicious activities such as failed login attempts, brute-force attacks, and unauthorized access attempts, and create alerts for abnormal behaviors.

## Simulate SSH Brute force attack
```
hydra -l root -P /path/to/passwordlist.txt ssh://<target-ubuntu-ip>
```
