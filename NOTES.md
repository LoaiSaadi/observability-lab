# Learning Log

## Phase 1 — Infrastructure

### What I Did
- Set up three AWS EC2 instances: `monitor-01`, `app-01`, and `app-02`.
- Configured hostnames and `/etc/hosts` so the servers can communicate using names instead of IP addresses.
- Set the timezone to `Asia/Jerusalem` on all servers.
- Created SSH keys and tested connections from `monitor-01` to both application servers.
- Configured AWS Security Groups and UFW to control network access.
- Installed Docker Engine and Docker Compose on `monitor-01`.
- Connected the monitoring server to my private GitHub repository using SSH.

### What I Learned
- Security Groups and UFW are two different layers of network security.
- SSH keys allow secure connections between servers without using account passwords.
- Keeping server configurations in Git makes it easier to move the setup to new machines.

---

## Phase 2 — Prometheus

### What I Did
- Deployed Prometheus using Docker Compose.
- Configured it to collect metrics every 15 seconds.
- Added a persistent Docker volume and set data retention to 15 days.
- Accessed Prometheus using an SSH tunnel instead of exposing it publicly.
- Tested PromQL queries such as `up`, `rate()`, and `prometheus_tsdb_head_series`.
- Validated the configuration with `promtool`.
- Tested reloading the configuration without restarting the container.

### What I Learned
- Prometheus collects metrics by scraping configured targets.
- PromQL is used to query and analyze the collected metrics.
- Docker volumes keep the monitoring data even when containers are removed and recreated.

---

## Phase 3 — Node Exporter

### What I Did
- Installed Node Exporter on `monitor-01` and `app-01`.
- Created a dedicated Linux user for the service.
- Configured Node Exporter to run automatically using systemd.
- Exposed system metrics on port `9100`.
- Allowed access from the monitoring server using UFW and AWS Security Groups.
- Added both servers to Prometheus and confirmed their targets were UP.

### What I Learned
- Node Exporter collects Linux system metrics such as CPU, memory, disk, and network usage.
- Prometheus connects to Node Exporter to collect these metrics.
- A running service doesn't always mean Prometheus can reach it. Firewall rules and network connectivity also matter.

---

## Phase 4 — Grafana

### What I Did
- Deployed Grafana using Docker Compose.
- Connected Grafana to Prometheus as its data source.
- Created an Infrastructure Overview dashboard to monitor the servers.
- Configured dashboards and data sources using provisioning files.
- Stored dashboard JSON files in Git.
- Used a persistent Docker volume to keep Grafana data.

### What I Learned
- Prometheus collects and stores metrics, while Grafana helps visualize them.
- File provisioning lets me manage dashboards through configuration files instead of creating everything manually.
- Docker bind mounts make the dashboard files available inside the Grafana container.
- Grafana automatically checks for dashboard file changes and updates the UI.

---

## Phase 5 — Nginx Monitoring

### What I Did
- Installed Nginx on `app-01` and `app-02`.
- Configured the Nginx status endpoint locally on port `8080`.
- Installed Nginx Prometheus Exporter on `app-01`.
- Configured the exporter as a systemd service on port `9113`.
- Allowed monitoring access only from `monitor-01`.
- Added the Nginx target to Prometheus and confirmed it was UP.
- Created an Nginx dashboard in Grafana.

### What I Learned
- Nginx provides basic connection and request statistics through `stub_status`.
- Nginx Prometheus Exporter converts those statistics into metrics that Prometheus understands.
- I can check whether Nginx is working by querying the `nginx_up` metric.
- The status endpoint should stay local, while the exporter provides metrics to the monitoring server.

---

## Phase 6 — Telegraf

### What I Did
- Installed Telegraf on `app-02`.
- Replaced the large default configuration with a simple configuration containing only what I needed.
- Configured seven inputs: CPU, memory, disk, disk I/O, network, system, and Nginx.
- Set the collection interval to 15 seconds.
- Configured Telegraf to expose Prometheus metrics on port `9273`.
- Tested the configuration and confirmed the service was running.
- Added Telegraf as a Prometheus target and confirmed it was UP.
- Created a `Telegraf Hosts` dashboard with CPU, memory, disk, and Nginx connection graphs.
- Saved the Telegraf configuration and dashboards in Git.

### Node Exporter vs Telegraf

| Node Exporter | Telegraf |
|---|---|
| Mainly collects Linux system metrics | Collects system and application metrics |
| Exposes metrics on port `9100` in my lab | Exposes metrics on port `9273` in my lab |
| Simple and focused on host monitoring | More flexible through input plugins |
| Example: `node_cpu_seconds_total` | Example: `cpu_usage_idle` |

### What I Learned
- Both tools can provide system metrics, but they use different metric names.
- Node Exporter is a simple choice when I only need Linux host metrics.
- Telegraf is useful when I want to collect system and application metrics using a single agent.
- I used Node Exporter on `monitor-01` and `app-01`, and Telegraf on `app-02`.
- Prometheus can collect metrics from both tools at the same time.

---

## Migration to the New EC2 Instances

I started this lab on temporary EC2 instances and later moved the setup to three new instances.

| New EC2 Instance | Hostname |
|---|---|
| `Loai-training-monitoring` | `monitor-01` |
| `Loai-training-agent-1` | `app-01` |
| `Loai-training-agent-2` | `app-02` |

### What I Did
- Reconfigured the hostnames, networking, SSH access, and firewall rules.
- Installed Docker and restored the monitoring setup from GitHub.
- Updated the Prometheus targets to use the new private IP addresses.
- Reinstalled the exporters and Telegraf on the new servers.
- Confirmed all five Prometheus targets were UP.
- Tested `docker compose down` followed by `docker compose up -d`.
- Confirmed that Prometheus data and Grafana dashboards were still available after recreating the containers.

### Notes
- The monitoring server currently has a small root disk. I decided to leave it unchanged for now.
- I want to keep the setup easy to recreate on another server using Git and configuration files.
- The migration changes are in the `feat/migrate-new-ec2` branch, with a PR opened for merging into `main`.
