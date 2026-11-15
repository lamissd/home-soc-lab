# Attack plan

This table lists the attacks I will run from Kali against `victim`, and what I find afterward.

| # | Attack | Tool | MITRE technique | Wazuh detected? | Notes |
|---|---|---|---|---|---|
| 1 | Port scan | Nmap | T1046 | No | Ran at 23:41. The 8 events in that window (Valid Accounts, Remote Services, 2x Authentication success) correspond to my own SSH login to victim, not the scan itself. Nmap -sV generates no authentication or file-integrity activity, so Wazuh's default rules don't detect it. Would need a network IDS (e.g. Suricata) integration to catch port scans. |
| 2 | SSH brute force | Hydra | T1110 | Yes | Ran at 00:28. 5 failed attempts then 1 success (password "wazuh"). Wazuh logged each failure and the success as separate "Authentication" events. || 3 | Successful login | ssh | T1078 | | |
| 3 | Successful login | ssh | T1078 | Yes | Logged in as lamis using credentials found via Hydra. Detected via PAM session rules (5501). |
| 4 | Privilege escalation | sudo | T1548 | Yes | Ran `sudo whoami` at 13:24, confirmed root access. Detected: rule 5402 "Successful sudo to ROOT executed". |
| 5 | Persistence: new user | useradd | T1136 | No | Created user "backdoor". No alert appeared, /etc/passwd not in default FIM watch list. |
| 6 | Persistence: cron job | crontab | T1053 | No | Added cron job. No alert appeared, cron files not in default FIM watch list. |
## How I'll fill this in
After each attack, I'll check the Wazuh dashboard (Security Events) filtered to the `victim` agent, and write:
- **Yes** if an alert appeared, with the rule name
- **No** if nothing appeared
- A short note on what I think explains the result

- - [Attack 1: Nmap port scan](01-nmap-scan.md)
- - [Attack 2: SSH brute force with Hydra](attacks/02-hydra-bruteforce.md)
- - [Attack 3: Login + privilege escalation](attacks/03-privilege-escalation.md)
