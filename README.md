# Home SOC Lab

A hands-on learning project: building a small Security Operations Center (SOC) at home to detect attacks with an open-source SIEM.

## Objective

Learn blue-team fundamentals by building and attacking my own lab:

- Deploy a SIEM (**Wazuh**) and connect a monitored machine to it
- Simulate realistic attacks from Kali Linux
- Analyze what the SIEM detects, and what it **misses**
- Write custom detection rules
- Map each attack to the **MITRE ATT&CK** framework
- Document everything so the work is reproducible

## Tools

| Tool | Role |
|---|---|
| VirtualBox | Virtualization platform |
| Kali Linux | Attacker machine |
| Ubuntu Server (LTS) | Wazuh server and victim machine |
| Wazuh | SIEM / XDR: log collection, detection, alerting |

All tools are free and open-source (or free to use).

## Lab Architecture

All machines run in VirtualBox on an isolated NAT Network (`soc-lab`, `10.0.2.0/24`).

| Machine | Role | RAM |
|---|---|---|
| `kali` | Attacker | 2 GB |
| `wazuh-server` | SIEM (manager, indexer, dashboard) | 4 GB |
| `victim` | Monitored Ubuntu Server with Wazuh agent | 1 GB |

Architecture diagram: *coming soon* (`docs/images/`).

[View the architecture diagram](docs/architectureSOC.html)

## Attack Scenarios (planned)

1. Network scan (Nmap)
2. SSH brute force
3. Successful login and privilege escalation
4. Persistence (new user, cron job)
5. Sensitive file modification

For each scenario I document: the command used, what Wazuh alerted on, what it missed, the MITRE ATT&CK technique, and how to reduce the risk.

## Repository Structure

```
home-soc-lab/
├── README.md
├── docs/            Setup guides, diagrams, screenshots
├── attacks/         Attack scenarios and results
├── detections/      Custom Wazuh detection rules
└── mitre-mapping.md Attack-to-ATT&CK mapping
```

## Progress

- [x] Repository created
- [x] NAT Network configured
- [x] Ubuntu Server VMs created
- [x] Wazuh installed
- [x] Agent deployed on victim
- [x] Attack scenarios executed
- [x] Custom detection rules written
- [x] MITRE ATT&CK mapping completed
- [ ] Final write-up

## Legal and Ethical Notice

**All attacks in this project are performed exclusively on machines that I own, inside an isolated virtual network that is not exposed to the Internet.**

Do not use anything in this repository against systems you do not own or do not have explicit written permission to test. Unauthorized access to computer systems is illegal.

No real credentials, personal data, or employer data are published in this repository. All passwords and IPs shown are lab-only.

## Author

Lamiss Dachraoui, engineering student in computer science at ENSI, Tunisia.
Learning cybersecurity with a focus on detection and cloud security.

https://www.linkedin.com/in/lamiss-dachraoui-811796292/

## License

MIT
