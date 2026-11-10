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
