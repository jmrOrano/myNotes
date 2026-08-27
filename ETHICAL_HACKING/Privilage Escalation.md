## Privilege Escalation
Techniques used to gain higher-level access than what was originally granted, by abusing misconfigurations rather than exploiting code vulnerabilities.

### Cron Job / Scheduled Task Misconfiguration
---
Reference Idea:
- [[Bandit_Wargames#Level 21 to 22]]

>**Core idea:**
```
A cron job runs automatically as a higher-privileged user (e.g. bandit22).
If that script writes sensitive output (like a password) to a world-readable
file, or if the script itself is readable by a lower-privileged group, that
lower-privileged user can read the script to understand the leak — then
capture the secret without ever needing direct access to the privileged file.
```

>**Example scenario:**
```
A cron job owned by a service account regularly writes a temp file with
loose permissions (644) instead of restricting it (600). An attacker with
low-level shell access notices the job in /etc/cron.d/, reads the script
(if group-readable), and grabs the leaked secret the next time the job runs.
```

>**Attacker's checklist**
- Check `/etc/cron.d/`, `/etc/crontab`, and `crontab -l` for scheduled jobs
- Identify which jobs run as higher-privileged users
- Check permissions on the script itself — is it readable by your group?
- Check what the script *writes* and *where* — temp files, logs, world-readable output
- Time your read to when the cron job actually runs (cron jobs are time-based)

>**How to prevent**
- Scripts run by cron should write secrets with strict permissions (`600`, owner-only)
- Cron scripts themselves should not be group/world-readable if they contain logic tied to privileged accounts
- Avoid writing secrets to `/tmp` or other shared-access directories at all
- Apply principle of least privilege — even automation should follow it

**Reference:**
- [[Tools#**Cron**]]
- [[User Management#chmod]]
