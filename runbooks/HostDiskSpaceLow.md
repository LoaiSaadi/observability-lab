# Runbook — HostDiskSpaceLow

**Severity:** Warning

**Condition:** Less than 15% free disk space for 5 minutes

## What happened?

Prometheus detected low available disk space on the monitored server's root filesystem (`/`). This may be caused by growing logs, temporary files, or application data.

## How to check

1. Check disk usage:
   ```bash
   df -h /
   ```
2. Identify directories using the most space:
   ```bash
   sudo du -xhd1 / 2>/dev/null | sort -h
   ```
3. Check recent large files:
   ```bash
   sudo find /var/log -type f -size +100M -ls
   ```

## How to fix

Identify what is consuming disk space and determine whether it can be safely cleaned.

**Do not delete application data, logs, or system files without approval. Escalate to the responsible team if disk space cannot be safely recovered.**

## Verify

- Free disk space is above 15%.
- The alert is `Resolved` in MoovingON AI.
