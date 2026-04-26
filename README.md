Wazuh SOC Lab – SSH Brute Force Detection
Author

Mohamed Lamine Krina

Location

Warsaw, Poland

Overview

This project simulates a real-world SSH brute force attack and demonstrates how a Security Operations Center (SOC) analyst can detect, analyze, and respond to the attack using:

Wazuh SIEM (Detection and Analysis)
Hydra (Attack Simulation)
Nmap (Reconnaissance)
Fail2Ban (Automated Defense)

The lab was performed in a controlled VirtualBox environment for educational purposes.

Lab Environment
Component	Description
Attacker	Windows Host (Nmap, Hydra)
Target	Ubuntu VM – 192.168.56.101
SIEM	Wazuh Manager + Agent
Defense	Fail2Ban
Network	VirtualBox Host-Only
Architecture
Attacker (Nmap + Hydra)
        ↓  Brute Force Attack
Target VM (SSH Server - Port 22)
        ↓  Failed Login Logs
/var/log/auth.log
        ↓  Log Collection
Wazuh Agent
        ↓  Log Forwarding
Wazuh Manager
        ↓  Rule Matching & Analysis
Wazuh Dashboard
Phase 1 – Reconnaissance (Nmap)

Before launching the attack, reconnaissance was performed to identify open services.

Command:
nmap -p 22 192.168.56.101
Result:
Port 22 (SSH) is open
Target is reachable
SSH service confirmed
Advanced Scan (Nmap Script)
nmap -p 22 -Pn --script ssh-brute 192.168.56.101
Purpose:
Simulate authentication attempts
Validate SSH responsiveness
Generate initial log activity
Phase 2 – Attack Simulation (Hydra)

After confirming SSH availability, a brute force attack was executed.

Command:
hydra -l agent -P passwords.txt 192.168.56.101 ssh -t 4 -V
Explanation:
Parameter	Meaning
-l agent	Target username
-P passwords.txt	Password list
ssh	Target service
-t 4	Parallel threads
-V	Verbose mode
Result:
More than 20 failed login attempts generated
Continuous authentication failures logged
Realistic brute force behavior simulated
Phase 3 – Log Generation

All attack activity was recorded in:

/var/log/auth.log
Example Logs:
Failed password for agent from 192.168.56.101 port XXXX ssh2

These logs form the basis for detection.

Phase 4 – Detection (Wazuh SIEM)

Wazuh analyzed the logs and generated alerts.

Detection Details:
Field	Value
Log Source	/var/log/auth.log
Rule ID	5760
Description	SSH authentication failed
Severity	Medium
Analysis

Wazuh identified:

Repeated failed login attempts
Same source IP
Pattern consistent with brute force attack

This indicates malicious behavior rather than normal login failure.

MITRE ATT&CK Mapping
Category	Value
Technique	T1110 – Brute Force
Tactic	Credential Access

This mapping connects the attack to real-world threat intelligence frameworks.

Phase 5 – Defense (Fail2Ban)

Fail2Ban automatically responded to the attack.

How it Works:
Monitors /var/log/auth.log
Detects repeated failures
Applies firewall ban
Verification:
sudo fail2ban-client status sshd
Result:
Total failed attempts: approximately 21
Banned IP: 192.168.56.101
Status: Active ban
Full SOC Workflow
Recon → Attack → Log Generation → Detection → Response
Stage	Tool
Recon	Nmap
Attack	Hydra
Logs	Linux auth.log
Detection	Wazuh
Response	Fail2Ban
Screenshots

See /screenshots folder:

Nmap scan
Hydra attack
SSH logs
Wazuh alerts
Fail2Ban status
Key Learnings
Importance of reconnaissance before attacks
How brute force attacks generate detectable patterns
Role of SIEM in log analysis
Difference between raw logs and security alerts
Automated defense with Fail2Ban
MITRE ATT&CK improves threat understanding