# monitor-01

Central monitoring server running on Docker Engine + Docker Compose.

## Current Services

### Prometheus
- Image: `prom/prometheus:v3.15.1`
- Port: `9090` (localhost only)
- Configuration: `prometheus/prometheus.yml`
- Persistent volume: `monitor-01_prometheus_data`
- Retention: 15 days
- Status: Running

## Planned Services

- Grafana
- Loki
- Alertmanager

## Next Step

Install Node Exporter to collect host metrics from `monitor-01` and `app-01`.
