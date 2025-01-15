## Objective: Investigating Unauthorized SSH Access Attempts Using ELK SIEM
To investigate unauthorized SSH access attempts on a target Linux machine using ELK SIEM with Fleet (Elastic Agent), visualize suspicious activities such as failed login attempts, brute-force attacks, and unauthorized access attempts, and create alerts for abnormal behaviors.

- `Detailed level`: Low
- `Difficulty level`: Medium



## Simulate SSH Brute force attack
```
hydra -l root -P /path/to/passwordlist.txt ssh://<target-ubuntu-ip>
```

## Detection and Investigation on Elastic

- Once logs are flowing into Elasticsearch through Fleet Agent, go to **Kibana > Discover** to check the incoming logs.
- Use a query such as:
    
 ```
    event.outcome: "failure"  and process.name : "sshd" and event.category: "authentication" 
    
 ```
