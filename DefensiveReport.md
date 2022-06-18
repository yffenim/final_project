# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology Diagram by Helder Luis 

![diagram](https://raw.githubusercontent.com/yffenim/final_project/main/NetworkTopology.png)


### Network Topology

```
The following machines were identified on the network:
Name: ML-RefVm-684427
Operating System: Windows 10
Purpose: Hyper-V host
IP Address: 192.168.1.1
Name: ELK
Operating System: Ubuntu 18.04.4
Purpose: ELK server
IP Address: 192.168.1.100
Name server1
Operating System: Ubuntu 18.04.1
Purpose: Apache httpd 2.4.29
IP Address: 192.168.1.105
Name: target1
Operating System: Debian 8
Purpose: Apache httpd 2.4.10, Samba smbd server
IP Address: 192.168.1.110
Name: target2
Operating System: Debian 8
Purpose: Apache httpd 2.4.10, Samba smbd server
IP Address: 192.168.1.115
Name Kali
Operating System: Kali 5.4.0
Purpose: Attacker machine
IP Address: 192.168.1.90 (edited) 
```

### Description of Targets

The target of this attack was: `Target 1`

Target 1 is an Apache web server at IP address `192.168.1.110` running Wordpress and has ports 22, 80, 111, 139 and 445 open. To monitor this, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

### ALERT 1: Excessive HTTP Errors

`WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 65 FOR THE LAST 5 minutes.`

**Implemention:**

**Metric:** `WHEN count() GROUPED OVER top 5 'http.response.status_code'
**Threshold:** greater than 65 per 5 minutes`
**Vulnerability Mitigated:** Website/server is being queried repeatedly, possible brute force or DDOS attack is happening
**Reliability:** If there is a sudden surge of errors detected, we can reliably consider this an IOA (indicator of attack).

![excess]()

### ALERT 2: HTTP Request Size Monitor

`WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute`

**Implemention:**

**Metric:** `WHEN sum() of http.request.bytes OVER all documents`
**Threshold:** IS ABOVE 3500
**Vulnerability Mitigated:** Website scan, vulnerability enumeration, data exfiltration, server data access
**Reliability:** If there is suspiciously High data transfer to one external IP address, we can reliably suggest that this is an IOA of malicious activity

![size]()

### ALERT 3: CPU Usage Monitor

`WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.08 FOR THE LAST 5 minutes`

**Implemention:**

**Metric:** `WHEN max() OF system.process.cpu.total.pct OVER all documents`
**Threshold:** IS ABOVE 0.08
**Vulnerability Mitigated:** Elevated use of system resources threatens system availability
**Reliability:** High system resource consumption anomalies can be an IOA, however, this can easily be a false positive depending if there are events taking place with more users or network activity than usual.

_TODO Note: Explain at least 3 alerts. Add more if time allows._

### Suggestions for Going Further (Optional)

- Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain _how_ to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:


**1. Weak Authentication Credentials (Passwords)**

**We recomment implementing:**
- Password policy regarding complexity and cycling (create new passwords every predetermined length of time)
- MFA, ideally with an app and not a phone number
- Account lockout after 5 unsuccessful login attempts
- Salt and Pepper password hashes to strength encryption and prevent rainbow table attacks
- Use an authentication service to manage credentials instead of storing them on DB
- Database segregation - use a different database for storing credentials 
- Keep wordpress and plugins updated, scan for security vulnerabilities

**Why it works:**
Since the focus is weak authentication credentials, most of our suggestions help one to either strengthen the passwords or prevent any additional passwords from being discovered in the event of a breach. it is also important to keep applications updated as many patches contain security fixes.

**2. Network Enumeration (And Server/Database Access)**

**We recommend implementing:**
- Keep software updated
- Network segmentation so that access is restricted between domains within a network
- Install host-level firewall to monitor traffic 
- Mask API form responses so that failed password guesses are not revealed

**Why it works**
In the event of a breach, we need to practice harm reduction which is done by network segmentation. In order to prevent the breach, we recommend minimalizing the data that can be discovered from enumeration which would be accommplished by the other suggestions.

**3. Data Exfiltration**

**We recommend implementing:**
- Database authentcation 
- Database segmentation 
- IP whitelisting 
- Firewalls 
- Install Intrusion Detection/Prevention Systems

**Why it works**
To detect and mitigate Data Exfiltration, we need to establish a boundary between the external world and our network via Firewalls, IP whitelisting, and IDS/IPS systems. In the event of a breach, having the database segmented reduces the damage. 
