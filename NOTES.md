# Learning Log

This is a short summary of what I've worked on and learned while building my observability lab.

## Phase 1 — Infrastructure

### What I Did
- Set up three AWS EC2 instances: `monitor-01`, `app-01`, and `app-02`.
- Configured hostnames, SSH access, Security Groups, UFW, and timezones.
- Installed Docker and Docker Compose on the monitoring server.
- Connected the project to a private GitHub repository.

### What I Learned
- Security Groups and UFW provide different layers of network security.
- SSH keys make communication between servers easier and more secure.
- Keeping configurations in Git helps me recreate the setup later.

---

## Phase 2 — Prometheus

### What I Did
- Deployed Prometheus with Docker Compose.
- Set up 15-second scraping, a persistent volume, and 15-day retention.
- Accessed Prometheus through an SSH tunnel.
- Tested PromQL, configuration validation, and configuration reloads.

### What I Learned
- Prometheus collects and stores metrics from configured targets.
- PromQL helps me query and analyze those metrics.
- Docker volumes keep data even when containers are recreated.

---

## Phase 3 — Node Exporter

### What I Did
- Installed Node Exporter on `monitor-01` and `app-01`.
- Configured it as a systemd service on port `9100`.
- Updated firewall rules and added both servers to Prometheus.

### What I Learned
- Node Exporter provides Linux system metrics like CPU, memory, disk, and network usage.
- A service can be running but still unreachable because of firewall or network rules.

---

## Phase 4 — Grafana

### What I Did
- Deployed Grafana and connected it to Prometheus.
- Created an Infrastructure Overview dashboard.
- Configured data sources and dashboards using provisioning files.
- Stored the dashboard JSON files in Git.

### What I Learned
- Grafana helps visualize metrics collected by Prometheus.
- File provisioning lets me manage dashboards through Git instead of relying only on the UI.
- Bind mounts allow Grafana to read dashboard files directly from the server.

---

## Phase 5 — Nginx Monitoring

### What I Did
- Installed Nginx on both application servers.
- Enabled `stub_status` locally.
- Installed Nginx Prometheus Exporter on `app-01`, using port `9113`.
- Added Nginx monitoring to Prometheus and Grafana.

### What I Learned
- `stub_status` provides basic Nginx statistics.
- The exporter converts those statistics into Prometheus metrics.
- I can use `nginx_up` to check whether Nginx is responding.

---

## Phase 6 — Telegraf

### What I Did
- Installed Telegraf on `app-02`.
- Configured CPU, memory, disk, disk I/O, network, system, and Nginx inputs.
- Exposed metrics on port `9273` and added the target to Prometheus.
- Created a Telegraf dashboard in Grafana.

### What I Learned
- Node Exporter is a simple choice for Linux host metrics.
- Telegraf is more flexible and supports system and application metrics through plugins.
- Both tools work with Prometheus, but some metric names are different.

---

## Phase 7 — Loki and Grafana Alloy

### What I Did
- Deployed Loki with Docker Compose, persistent storage, and 7-day log retention.
- Connected Loki to Grafana as a second data source.
- Installed Alloy on `app-01` and `app-02`.
- Configured Alloy to collect Nginx access/error logs and systemd journal logs.
- Verified that logs from both servers reach Loki.
- Practiced LogQL to find HTTP 404 errors and count requests by status code.
- Added Nginx Logs and Requests by Status Code panels to the Nginx dashboard.

### What I Learned
- Prometheus handles metrics, while Loki handles logs.
- Alloy collects and sends logs to Loki.
- Nginx logs show HTTP requests, while systemd journal logs show system and service events.
- LogQL lets me filter logs and turn them into useful graphs.
- Labels like `host`, `job`, and `log_type` make it easy to separate data from different servers.
- Alloy can also collect host metrics, potentially replacing a separate Node Exporter service. I tested the collector briefly but skipped the Remote Write bonus.

---

## Migration to New EC2 Instances

I originally started this lab on temporary EC2 instances and later moved it to three new servers in another AWS region.

### What I Did
- Reconfigured SSH, hostnames, networking, and firewall rules.
- Restored the monitoring setup using the files stored in Git.
- Updated Prometheus targets to use the new private IPs.
- Reinstalled the exporters and Telegraf.
- Verified that all five Prometheus targets were UP.
- Tested container recreation and confirmed the monitoring data remained available.

### What I Learned
- Having the setup in Git makes migration much easier.
- Server addresses and access rules need to be reviewed when moving to new infrastructure.
- Persistent volumes help protect monitoring data when containers are recreated.

### Notes
- The EC2 instances are temporary until we move to the real servers.
- I'm keeping the configuration simple and reusable so the lab will be easy to rebuild later.

---

## Phase 8 — Alerting and MoovingON AI

### What I Did
- Deployed Alertmanager using Docker Compose.
- Created Prometheus alert rules for CPU, memory, disk, and service availability.
- Configured Loki Ruler for NGINX HTTP 5xx log alerts.
- Connected Alertmanager to MoovingON AI using a webhook.
- Tested alert firing and recovery for all eight alerts.
- Verified that notifications were received and resolved correctly.

### What I Learned
- Prometheus and Loki can generate alerts independently.
- Alertmanager handles routing, grouping, and notifications.
- Alert labels must contain the information required by the receiving system.
- Alert recovery is as important to test as alert firing.

---

## Phase 9 — Security Hardening

### What I Did
- Configured HTTPS for Grafana using NGINX as a reverse proxy.
- Created TLS certificates and configured certificate trust.
- Enabled Basic Authentication for Prometheus and Alertmanager.
- Secured Node Exporter with TLS and Basic Authentication.
- Restricted monitoring ports using AWS Security Groups and UFW.
- Kept passwords, certificates, and secrets outside Git.
- Added sensitive-value redaction to Alloy log processing.

### What I Learned
- TLS encrypts communication and helps verify server identity.
- Basic Authentication controls access to protected endpoints.
- Security Groups and UFW provide different security layers.
- Updating credentials requires changes on both the client and server.
- Services without built-in authentication need network restrictions or a secure proxy.

---

## Phase 10 — Documentation and Recovery

### What I Did
- Updated the main README with architecture and deployment instructions.
- Documented server roles, ports, security, and configuration files.
- Created troubleshooting documentation.
- Added runbooks for the eight alert scenarios.
- Added the Monitoring of the Monitoring dashboard.
- Tested Grafana dashboard recovery using provisioning.
- Committed documentation changes through feature branches and pull requests.

### What I Learned
- Documentation makes a system easier to maintain and rebuild.
- Runbooks help engineers respond consistently to incidents.
- Provisioning allows Grafana dashboards to be restored from configuration files.
- A clean Git workflow makes changes easier to review and track.

---

## Problems Faced and How I Solved Them

### Problem 1 — Prometheus Could Not Scrape monitor-01

Problem:
Node Exporter was running on monitor-01, but Prometheus
showed its target as DOWN.

Root Cause:
UFW blocked traffic from the Docker network to port 9100
on monitor-01.

Solution:
I checked the service, port, and firewall rules.
I allowed the required Docker subnet to access Node Exporter
on port 9100 and confirmed that the target became UP.

What I Learned:
A running service can still be unreachable because of
network or firewall restrictions.

### Problem 2 — Loki Alerts Were Missing from Prometheus

Problem:
The NginxHigh5xxLogs alert was firing, but I could not
find it on the Prometheus Alerts page.

Root Cause:
The alert was generated by Loki Ruler, not Prometheus.
They are separate alert evaluation systems.

Solution:
I checked the Loki Ruler and Alertmanager.
I configured an Alertmanager data source in Grafana
to view the alerts and verified their delivery.

What I Learned:
Prometheus and Loki can evaluate different alert rules
and both can send notifications through Alertmanager.

### Problem 3 — Git Push Was Blocked by AWS Networking

Problem:
I could not push the final documentation branch to GitHub.
The SSH connection on port 22 timed out.

Root Cause:
The EC2 Security Group outbound rules were restricted
and did not allow the required GitHub SSH connection.

Solution:
I checked outbound connectivity and Security Group rules.
I allowed the required destination on port 443 and used
GitHub SSH over TCP port 443 through ssh.github.com.
The push completed successfully.

What I Learned:
Outbound firewall restrictions can block Git operations.
I learned to troubleshoot DNS, ports, SSH, and AWS networking.
