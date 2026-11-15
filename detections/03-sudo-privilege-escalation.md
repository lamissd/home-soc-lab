# Default detection: Sudo privilege escalation (T1548)

## What happened
After logging in as `lamis` using credentials found via Hydra (see [attacks/03-privilege-escalation.md](../attacks/03-privilege-escalation.md)), I ran `sudo whoami` to confirm root access. Wazuh logged this by default.

## Which rules caught it
Three events fired at the same moment (13:24:57), all level 3:

| Rule ID | Description |
|---|---|
| 5501 | PAM: Login session opened |
| 5402 | Successful sudo to ROOT executed |
| 5502 | PAM: Login session closed |

![Events table](../docs/images/34-wazuh-after-privesc.png)

## Why this worked by default
PAM (Pluggable Authentication Modules) and `sudo` both write directly to `/var/log/auth.log` on Ubuntu. Wazuh's agent reads this file continuously, so session opens/closes and sudo usage are covered out of the box, no extra configuration needed, same as the SSH brute-force detection.

## A gap I noticed
All three events are **level 3**, a low severity. A successful "sudo to ROOT" right after a login that itself followed a brute-force attack (detected minutes earlier, see [02-ssh-bruteforce-default-rule.md](02-ssh-bruteforce-default-rule.md)) should arguably be treated as more serious in context. Individually these are normal admin actions; together, they represent a full compromise chain.

## Possible improvement
Wazuh supports **correlation rules**, rules that look for a sequence of lower-level events within a time window and raise a higher-severity alert when the pattern matches. A custom correlation rule could combine:
1. Multiple SSH authentication failures (brute force)
2. Followed by an authentication success
3. Followed by a sudo-to-root event

...and raise this combination to a high-severity alert ("possible successful credential-stuffing attack with privilege escalation"), rather than three separate low-level, easy-to-miss log lines.

## Status
Documented. Default rules work individually; a correlation rule to connect the full attack chain is a planned next step.
