# Runbook — HostDiskSpaceCritical

**Severity:** Critical

**Condition:** Less than 5% free disk space for 2 minutes

## What happened?

Prometheus detected critically low disk space on the monitored server's root filesystem (`/`). The server may run out of space, causing application failures, logging issues, or service interruptions.

## How to check

1. Check available disk space:
   ```bash
   df -h /
   ```
2. Identify directories consuming the most space:
   ```bash
   sudo du -xhd1 / 2>/dev/null | sort -h
   ```
3. Check recent large log files:
   ```bash
   sudo find /var/log -type f -size +100M -ls
   ```

## How to fix

Identify what is consuming disk space and check whether it can be safely cleaned.

**Do not delete logs, application data, or system files without approval. Escalate immediately to the responsible team if disk space cannot be recovered safely.**

## Verify

- Free disk space is above 5%.
- The alert is `Resolved` in MoovingON AI.
