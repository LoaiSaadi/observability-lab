# Runbook — HostHighCpu

**Severity:** Warning

**Condition:** CPU usage above 85% for 5 minutes

**Affected server:** Check the alert's instance label.

## What happened?

Prometheus detected high CPU usage on the monitored server. This may be caused by heavy processes, high traffic, or a service consuming too many resources.

## How to check

1. Check current CPU usage:
   ```bash
   top
   ```
2. Find the processes using the most CPU:
   ```bash
   ps aux --sort=-%cpu | head
   ```
3. Check the system load:
   ```bash
   uptime
   ```

## How to fix

Identify the process causing high CPU usage.

- If the load is expected, continue monitoring.
- If a service is consuming excessive CPU, investigate its logs and resource usage.

**Do not stop or restart critical services without approval. Escalate to the responsible team if the issue persists.**

## Verify

- CPU usage returns below 85%.
- The alert is `Resolved` in MoovingON AI.
