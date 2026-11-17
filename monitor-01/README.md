# monitor-01 — Central Monitoring Server

This server runs the central monitoring stack using Docker Compose. It collects metrics, stores logs, provides dashboards and sends alerts to MoovingON Stage.

## Services

| Service | Image | Port | Purpose |
|---|---|---|---|
| Prometheus | prom/prometheus:v3.15.1 | 9090 (localhost) | Collects metrics and evaluates alert rules |
| Grafana | grafana/grafana:13.2.3 | 3000 (localhost) | Dashboards and visualization |
| Loki | grafana/loki:3.7.9 | 3100 (private IP) | Stores logs sent by Alloy |
| Alertmanager | prom/alertmanager:v0.34.2 | 9093 (localhost) | Routes alerts to MoovingON Stage |

Node Exporter also runs directly on this server as a systemd service, exposing HTTPS metrics on port 9100.

NGINX runs on the host as an HTTPS reverse proxy for Grafana on port 443.

## Configuration Files

| Component | Configuration |
|---|---|
| Docker Compose | docker-compose.yml |
| Prometheus | prometheus/prometheus.yml |
| Alert rules | prometheus/rules/ |
| Alertmanager | alertmanager/alertmanager.yml |
| Loki | loki/loki.yml |
| Loki alert rules | loki/rules/ |
| Grafana data sources | grafana/provisioning/datasources/ |
| Grafana dashboard provisioning | grafana/provisioning/dashboards/ |
| Grafana dashboards | grafana/dashboards/ |
| NGINX reverse proxy | nginx/grafana.conf |

## Persistent Data

Docker named volumes store data for:

- Prometheus
- Grafana
- Loki
- Alertmanager

These volumes survive normal container recreation.

Do not run `docker compose down -v` unless you intentionally want to delete persistent data.

## Managing the Stack

Run these commands from `~/observability-lab/monitor-01`.

Check configuration:

    docker compose config -q

Check container status:

    docker compose ps

View recent service logs:

    docker compose logs --tail=50 <service>

Restart a specific service:

    docker compose restart <service>

## Grafana Dashboards

Five dashboards are stored in Git and loaded through file provisioning:

- Infrastructure Overview
- NGINX Overview
- Telegraf Hosts
- Node Exporter Full
- Monitoring of the Monitoring

Provisioning is configured to check dashboard files every 30 seconds.

## Security

- Grafana is accessed through HTTPS on port 443.
- Prometheus and Alertmanager use Basic Auth.
- Node Exporter uses TLS and Basic Auth.
- Loki is accessible on a private IP with restricted firewall access.
- Secrets are stored in local files and are excluded from Git.
- AWS Security Groups and UFW restrict network access.
- The outbound Security Group allows internal VPC communication and HTTPS to the current MoovingON Stage API IP addresses.

Important: Stage API IP addresses may change. The outbound allowlist must be reviewed if notifications stop working.

## Secrets

Copy `.env.example` to `.env` and provide the required values during setup.

Additional credentials, TLS certificates and web authentication configurations are stored in protected secrets directories.

Never commit `.env`, passwords, tokens or private keys to Git.

## Recovery

Configuration and provisioned dashboards are stored in Git. Persistent data and settings created manually in Grafana require separate backup or recreation.

See the root README and UPDATE_PROCEDURE.md for additional documentation.

Grafana recovery was validated on November 17, 2026 using a temporary empty Docker volume. Provisioned data sources and all four dashboards returned automatically. The original volume was restored and the temporary volume removed.
