# Runbook — HostHighMemory

**Severity:** Warning

**Condition:** Memory usage above 90% for 5 minutes

## What happened?

Prometheus detected high memory usage on the monitored server. This may be caused by memory-intensive processes, application memory leaks, or increased workload.

## How to check

1. Check available memory:
   ```bash
   free -h
   ```
2. Identify processes consuming the most memory:
   ```bash
   ps aux --sort=-%mem | head
   ```
3. Check for out-of-memory events:
   ```bash
   sudo journalctl -k --since "30 minutes ago" | grep -i 'out of memory\|oom'
   ```

## How to fix

Identify the process consuming excessive memory and investigate the cause.

If the issue is caused by a service, check its logs and restart it only if approved.

**Do not kill processes or restart critical services without approval. Escalate to the responsible team if memory usage remains high.**

## Verify

- Memory usage returns below 90%.
- The alert is `Resolved` in MoovingON AI.
