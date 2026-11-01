# Learning Log

## Phase 1 — Infrastructure

### What I Did
- Created 3 temporary AWS EC2 instances (`monitor-01`, `app-01`, `app-02`) with the required resources.
- Configured hostnames and `/etc/hosts` instead of DNS.
- Set the timezone to `Asia/Jerusalem`.
- Generated SSH keys and verified connectivity between `monitor-01` and both apps.
- Configured AWS Security Groups to restrict access.
- Enabled UFW on all servers, allowing SSH and outgoing traffic.
- Installed Docker Engine + Docker Compose on `monitor-01` only.
- Tested Docker using `hello-world`.
- Connected `monitor-01` to our private GitHub repository using SSH keys.

### Problems Solved
- Docker Compose wasn't available in Ubuntu's default APT repositories. Added the official Docker repository.
- SSH connections timed out after updating Security Groups. Fixed the inbound rules using the monitoring server's Security Group.

### Notes
- These EC2 instances are temporary until we get the real servers.
- We'll keep the same server roles and store configurations in Git for future migration.

## Phase 2 — Prometheus

### What I Did
- Deployed Prometheus using Docker Compose on `monitor-01`.
- Configured self-monitoring every 15 seconds.
- Added a persistent Docker volume with 15-day retention.
- Accessed the UI using an SSH tunnel without exposing port `9090` publicly.
- Tested PromQL queries: `up`, `prometheus_tsdb_head_series`, and `rate()`.
- Validated the configuration using `promtool`.
- Tested `docker compose down/up` and confirmed the volume remained.
- Enabled HTTP reload and tested `POST /-/reload` successfully.
- Committed the changes to a feature branch, opened a PR, and merged into `main`.

### Notes
- Prometheus is running and its target is UP.
- Still need to confirm historical metrics remain available after container recreation.
