# Observability Onboarding Lab

A hands-on observability platform built with Docker Compose
and Linux systemd services.

## Infrastructure

| Server | Role |
|--------|------|
| monitor-01 | Central monitoring server |
| app-01 | Nginx, node_exporter, nginx exporter, Alloy |
| app-02 | Nginx, Telegraf, Alloy |

## Monitoring Stack

- Prometheus - Metrics collection and storage
- Alertmanager - Alert routing and notifications
- Grafana - Dashboards and visualization
- Loki - Log storage
- Grafana Alloy - Log collection
- Telegraf - Infrastructure metrics
- Nginx - Application servers

## Deployment

Currently deployed on three temporary (from Free Tier account) AWS EC2 instances.

Configurations will be version-controlled to support
migration to the final servers.

## Progress

- [x] Phase 1 - Infrastructure (final checks pending)
- [ ] Phase 2 - Prometheus
- [ ] Phase 3 - Node Exporter
- [ ] Phase 4 - Grafana
- [ ] Phase 5 - Nginx
- [ ] Phase 6 - Telegraf
- [ ] Phase 7 - Loki + Alloy
- [ ] Phase 8 - Alertmanager
- [ ] Phase 9 - Security Hardening
- [ ] Phase 10 - Documentation
