# Runbook — NginxHigh5xxLogs

**Severity:** Warning

**Condition:** More than 20 HTTP 5xx responses within 5 minutes.

## What happened?

Loki detected a high number of HTTP 5xx errors in NGINX access logs. This may indicate an application failure, an unavailable backend, or an NGINX configuration issue.

## How to check

1. Check that NGINX is running:
   ```bash
   sudo systemctl status nginx
   ```

2. Check recent HTTP 5xx errors:
   ```bash
   sudo grep -E '" 5[0-9]{2} ' /var/log/nginx/access.log | tail -n 20
   ```

3. Open **Grafana → Explore → Loki** and run:
   ```logql
   {job="nginx",log_type="access"} |~ "\"[^\"]+\" 5[0-9]{2} "
   ```

## How to fix

Identify the affected application or endpoint using the access logs.

- If the backend is unavailable, check its service status and logs.
- If NGINX has a configuration issue, validate it using `sudo nginx -t`.
- Escalate to the responsible team if the errors continue or the root cause cannot be identified.

## Verify

- HTTP 5xx errors have stopped.
- The number of 5xx responses falls below the alert threshold.
- The alert is `Resolved` in MoovingON AI.
