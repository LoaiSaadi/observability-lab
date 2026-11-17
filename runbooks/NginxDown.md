# Runbook — NginxDown

**Severity:** Critical

**Server:** app-01

**Condition:** `nginx_up == 0` for 1 minute

## What happened?
Prometheus detected that the NGINX Exporter cannot reach the NGINX service on app-01. NGINX may be stopped, crashed, or experiencing an issue.

## How to check
1. Check NGINX status:
   ```bash
   systemctl status nginx
   ```
2. Check recent errors:
   ```bash
   sudo journalctl -u nginx -n 20
   ```
3. Check NGINX configuration:
   ```bash
   sudo nginx -t
   ```

## How to fix
If NGINX is stopped and its configuration is valid:

```bash
sudo systemctl start nginx
```

If it fails to start, review the logs and escalate to the responsible team.

## Verify
- NGINX is `active`.
- The alert is `Resolved` in MoovingON AI.
