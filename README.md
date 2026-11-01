# Observability Onboarding Lab

A hands-on observability platform using Docker Compose and Linux systemd services.

## Infrastructure

| Server | Role |
|---|---|
| monitor-01 | Central monitoring server |
| app-01 | Nginx, node_exporter, nginx exporter, Alloy |
| app-02 | Nginx, Telegraf, Alloy |

Currently running on 3 temporary AWS EC2 instances. We'll move to the real servers later.

## Monitoring Stack

- **Prometheus** — Metrics collection (deployed)
- **Alertmanager** — Alert routing (planned)
- **Grafana** — Dashboards (planned)
- **Loki** — Log storage (planned)
- **Alloy** — Log collection (planned)
- **Telegraf** — Infrastructure metrics (planned)
- **Nginx** — Application monitoring (planned)

## Prometheus

Running on `monitor-01` using Docker Compose.

- Scrape interval: 15s
- Retention: 15 days
- Port: 9090 (localhost only)
- Persistent Docker volume
- HTTP configuration reload enabled
- Self-monitoring target: UP

Access the UI through an SSH tunnel.

## Git Workflow

Feature branch → Commit → Push → Pull Request → Review → Merge into main.

## Progress

- [x] Phase 1 — Infrastructure (final checks pending)
- [x] Phase 2 — Prometheus (historical data verification pending)
- [ ] Phase 3 — Node Exporter
- [ ] Phase 4 — Grafana
- [ ] Phase 5 — Nginx
- [ ] Phase 6 — Telegraf
- [ ] Phase 7 — Loki + Alloy
- [ ] Phase 8 — Alertmanager
- [ ] Phase 9 — Security Hardening
- [ ] Phase 10 — Documentation
