# Detection gap: Nmap port scan (T1046)

## What happened
I ran a basic `nmap -sV` scan against `victim` (see [attacks/01-nmap-scan.md](../attacks/01-nmap-scan.md)). No alert was generated in Wazuh.

## Why Wazuh missed it
Wazuh's default setup relies on:
- **Log analysis** (parsing files like `/var/log/auth.log`, `/var/log/syslog`)
- **File Integrity Monitoring** (watching file changes)
- **Rootcheck / vulnerability detection** (periodic scans of the host itself)

None of these inspect raw network traffic. A port scan is a network-layer activity: it doesn't write anything to a log file or touch any monitored file, so there's nothing in Wazuh's default data sources for it to match against.

## What would close this gap
Wazuh can integrate with a network-based IDS such as **Suricata**, which inspects traffic directly and can detect scan patterns (many connections to different ports in a short window from one source). Suricata can then forward its alerts into Wazuh, so the scan would show up as an event in the same dashboard.

## What I could do without adding new tools
A lighter alternative is a **custom Wazuh rule based on connection attempts logged elsewhere** (e.g., if a local firewall like `ufw` or `iptables` logs denied/accepted connections, those logs could be parsed by Wazuh and a rule written to flag "many ports touched by one source IP in under N seconds").

## Status
Documented, not yet implemented. This is a planned next step: install and integrate Suricata, or configure `ufw` logging plus a custom rule.
