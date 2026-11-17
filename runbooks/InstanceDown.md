# Runbook — InstanceDown

**Severity:** Critical

**Condition:** `up == 0` for 1 minute

## What happened?

Prometheus cannot collect metrics from a monitored target. The server may be unreachable, the Exporter may be stopped, or there may be a network or authentication issue.

## How to check

1. Check the affected target in Prometheus under **Status → Targets**.
2. If the affected target is Node Exporter, check its service:
   ```
   systemctl status node_exporter
   ```
3. Check recent errors:
   ```
   sudo journalctl -u node_exporter -n 20
   ```

## How to fix

If Node Exporter is stopped:
```
sudo systemctl start node_exporter
```

**If the service is running but the target remains DOWN, check network connectivity, firewall rules, and authentication. Escalate if the issue persists.**

## Verify

- Node Exporter is `active`.
- The target is `UP` in Prometheus.
- The alert is `Resolved` in MoovingON AI.
