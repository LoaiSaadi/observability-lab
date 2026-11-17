# Troubleshooting Guide

Common problems and troubleshooting steps for the Observability Lab.

## 1. Prometheus Target DOWN

### What happened?

Prometheus cannot collect metrics from a monitored target.
Possible causes include a stopped exporter, network connectivity,
firewall restrictions, or authentication problems.

### How to check

1. Open Prometheus > Status > Targets.
2. Identify the DOWN target and read its error message.
3. Connect to the affected server and check the exporter service.

For Node Exporter:

```bash
sudo systemctl status node_exporter
sudo journalctl -u node_exporter -n 50 --no-pager
```

For NGINX Exporter on app-01:

```bash
sudo systemctl status nginx-exporter
curl http://127.0.0.1:9113/metrics
```

For Telegraf on app-02:

```bash
sudo systemctl status telegraf
curl http://127.0.0.1:9273/metrics
```

### How to fix

- Restore the stopped service after identifying the problem.
- Check AWS Security Groups and UFW.
- Verify network connectivity between monitor-01 and the target.
- For Node Exporter, verify HTTPS certificates and Basic Auth.
- Review the Prometheus scrape configuration.

### Verify

- The target shows UP in Prometheus.
- Metrics are available in Grafana.
- Related alerts recover in MoovingON AI.

---

## 2. No Logs in Loki

### What happened?

NGINX or systemd logs are missing from Grafana.
Possible causes include Alloy failures, file permissions,
network problems, or Loki being unavailable.

### How to check

On the affected application server:

```bash
sudo systemctl status alloy
sudo journalctl -u alloy -n 50 --no-pager
```

Check whether NGINX is writing logs:

```bash
sudo tail -n 10 /var/log/nginx/access.log
```

Check connectivity to Loki:

```bash
getent hosts monitor-01
curl http://monitor-01:3100/ready
```

In Grafana > Explore, select Loki and run:

```logql
{job="nginx",host="app-02"}
```

Replace app-02 with the affected hostname.

### How to fix

- Restore Alloy if it is stopped.
- Check Alloy configuration and service logs.
- Verify Alloy can read NGINX logs and the systemd journal.
- Confirm Loki is running on monitor-01.
- Check connectivity to TCP port 3100.
- Verify that the hostname and log labels are correct.

### Verify

- New logs appear in Grafana Explore.
- The correct host and log_type labels are present.
- NGINX logs appear in the Nginx Overview dashboard.

---

## 3. Alert Not Arriving in MoovingON AI

### What happened?

An alert is firing in Prometheus or Loki, but no notification
appears in MoovingON AI.

Possible causes include Alertmanager routing, notification
grouping delays, authentication failures, or network restrictions.

### How to check

1. Confirm that the alert is Firing in Prometheus or Loki.
2. Check whether Alertmanager received the alert.
3. Review Alertmanager logs on monitor-01:

```bash
cd ~/observability-lab/monitor-01
docker compose logs --tail=100 alertmanager
```

4. Check that the MoovingON Stage API is reachable:

```bash
curl -sS -o /dev/null -w '%{http_code}\n' https://api.stage.moovingon.ai/
```

An HTTP response such as 404 confirms that the server
was reachable; it does not confirm notification delivery.

5. Review Alertmanager routing, grouping, and silences.
6. Check the outbound AWS Security Group rules.

### How to fix

- Correct Alertmanager configuration or routing problems.
- Verify that the required credentials are available.
- Check whether the alert is silenced or inhibited.
- Check the outbound firewall rules for the Stage API.
- Remember that the Stage API IP addresses may change.

Alertmanager uses notification grouping.
Resolved notifications may not arrive immediately.

### Verify

- The alert appears in MoovingON AI.
- A controlled recovery produces a Recovered notification.
- Alertmanager logs show no notification errors.

---

## Escalation

If the service cannot be restored or the cause remains unclear,
escalate to the responsible team with:

- Affected server and service
- Alert name and time
- Error messages and relevant logs
- Troubleshooting actions already performed
