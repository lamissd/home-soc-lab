# Detection gap: Persistence via new user and cron job (T1136, T1053)

## What happened
I created a backup user account (`backdoor`) and a cron job on `victim` (see [attacks/04-persistence.md](../attacks/04-persistence.md)). No alert appeared in Wazuh for either action.

## MITRE ATT&CK techniques
- T1136 - Create Account
- T1053 - Scheduled Task/Job

## Why Wazuh missed it
Wazuh's File Integrity Monitoring (FIM) only watches a pre-configured list of directories and files, defined in the `<syscheck>` section of `ossec.conf`. By default, this list does not necessarily include `/etc/passwd`, `/etc/shadow`, or cron directories like `/var/spool/cron/crontabs/`.

Checked the current FIM configuration on `wazuh-server`:
```
sudo cat /var/ossec/etc/ossec.conf | grep -A 3 "<directories"
```

![FIM configuration check](FIMconfigcheck.png)

## Why this matters
This is a realistic and known blind spot. An attacker who gains initial access (as simulated in attacks 2-3, both of which were detected) can create persistence mechanisms that go completely unnoticed unless FIM is specifically configured to watch the right paths. A SIEM that catches the break-in but misses the backdoor gives a false sense of security.

## How to close this gap
Add the relevant paths to the `<syscheck>` section of `ossec.conf` on `wazuh-server`:
```xml
<directories realtime="yes">/etc/passwd,/etc/shadow,/etc/group</directories>
<directories realtime="yes">/var/spool/cron/crontabs</directories>
```
Then restart the Wazuh manager (and the agent's FIM scan) and re-run the persistence attack to confirm detection.

## Status
**Gap confirmed, then fixed and verified.**

Initial fix attempt failed because I edited the Wazuh **manager's** own `ossec.conf`, which only controls what the manager watches on itself, not what agents watch on monitored endpoints. The correct fix required editing the **shared agent configuration** (`/var/ossec/etc/shared/default/agent.conf`), which Wazuh pushes to all connected agents.

After correcting this and restarting both the manager and the agent, re-running the attack confirmed detection:

```
** Alert: syscheck,syscheck_entry_modified,syscheck_file,pci_dss_11.5,gpg13_4.11,gdpr_II_5.1.f,hipaa_164.312.c.1,...
File '/etc/passwd' modified
```

Confirmed via the raw alerts log (`/var/ossec/logs/alerts/alerts.log`) at 22:02:03, matching the exact moment I ran `useradd -m -s /bin/bash backdoor4`.

The cron job test confirmed detection as well, with a dedicated rule:

```
** Alert: syslog,cron,pci_dss_10.2.7,pci_dss_10.6.1,gpg13_4.13,gdpr_IV_35.7.d,hipaa_164.312.b,nist_800_53_AU.14,nist_800_53_AU.6,...
Rule: 2832 (level 5) -> 'Crontab entry changed.'
```

This rule is more specific than the generic `syscheck_entry_modified` alert that caught the `/etc/passwd` change, it's purpose-built to flag crontab modifications, confirming Wazuh has dedicated detection logic for this persistence technique once the file path is actually being monitored.

**Both persistence techniques tested in attack 4 are now detected by Wazuh.**

## Lesson learned
Wazuh distinguishes between the **manager's own FIM config** and the **shared configuration pushed to agents**. Editing the wrong one silently has no effect on what agents actually monitor, there's no error, it just doesn't work. This is an easy and realistic mistake to make when first configuring centralized monitoring for multiple endpoints.
