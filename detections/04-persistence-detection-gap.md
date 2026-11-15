# Detection gap: Persistence via new user and cron job (T1136, T1053)

## What happened
I created a backup user account and a cron job on `victim` (see [attacks/04-persistence.md](../attacks/04-persistence.md)). No alert appeared in Wazuh for either action.

## MITRE ATT&CK techniques
- T1136 - Create Account
- T1053 - Scheduled Task/Job

## Why Wazuh missed it
Wazuh's File Integrity Monitoring (FIM) only watches a pre-configured list of directories. By default, this list does not necessarily include `/etc/passwd`, `/etc/shadow`, or cron directories like `/var/spool/cron/crontabs/`.

Checked the current FIM configuration on wazuh-server:
```
[paste relevant output from ossec.conf]
```

![FIM config](FIMconfigcheck.png)

## Why this matters
This is a realistic and known blind spot: an attacker who gains initial access (as simulated in attacks 2-3) can create persistence mechanisms that go completely unnoticed unless FIM is specifically configured to watch the right paths.

## How to close this gap
Add the relevant paths to the `<syscheck>` section of `ossec.conf`:
```xml
<directories realtime="yes">/etc/passwd,/etc/shadow,/etc/group</directories>
<directories realtime="yes">/var/spool/cron/crontabs</directories>
```
Then restart the agent on `victim` and re-run the persistence attack to confirm detection.

## Status
Gap confirmed and documented. Fix planned: add syscheck directories and re-test.
