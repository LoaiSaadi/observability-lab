# Runbook — TelegrafNginxDown

**Severity:** Critical

**Condition:** Telegraf is reachable, but the `nginx_active` metric is missing for 1 minute.

## What happened?

Prometheus detected that Telegraf is no longer reporting NGINX metrics on the monitored server. NGINX may be stopped, or Telegraf may be unable to collect its metrics.

## How to check

1. Check NGINX and Telegraf services:
   ```bash
   sudo systemctl status nginx
   sudo systemctl status telegraf
   ```
2. Check the NGINX status endpoint:
   ```bash
   curl http://127.0.0.1:8080/stub_status
   ```
3. Check whether Telegraf exposes the metric:
   ```bash
   curl -s http://localhost:9273/metrics | grep '^nginx_active'
   ```

## How to fix

If NGINX is stopped, restart it:

```bash
sudo systemctl restart nginx
```

If NGINX is running but metrics are still missing, check the Telegraf logs:

```bash
sudo journalctl -u telegraf -n 50 --no-pager
```

**Escalate to the responsible team if NGINX cannot be restored or metrics remain unavailable.**

## Verify

- NGINX and Telegraf services are active.
- The `nginx_active` metric is available again.
- The alert is `Resolved` in MoovingON AI.
