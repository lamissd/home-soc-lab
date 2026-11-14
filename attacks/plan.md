# Attack plan

This table lists the attacks I will run from Kali against `victim`, and what I find afterward.

| # | Attack | Tool | MITRE technique | Wazuh detected? | Notes |
|---|---|---|---|---|---|
| 1 | Port scan | Nmap | T1046 | | |
| 2 | SSH brute force | Hydra | T1110 | | |
| 3 | Successful login | ssh | T1078 | | |
| 4 | Privilege escalation | sudo | T1548 | | |
| 5 | Persistence: new user | useradd | T1136 | | |
| 6 | Persistence: cron job | crontab | T1053 | | |

## How I'll fill this in
After each attack, I'll check the Wazuh dashboard (Security Events) filtered to the `victim` agent, and write:
- **Yes** if an alert appeared, with the rule name
- **No** if nothing appeared
- A short note on what I think explains the result
