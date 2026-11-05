# Observability Onboarding Lab

A hands-on observability platform using Docker Compose and Linux systemd services.

## Infrastructure

| Server | Role |
|---|---|
| monitor-01 | Central monitoring server |
| app-01 | Nginx, Node Exporter, Nginx Exporter, Alloy |
| app-02 | Nginx, Telegraf, Alloy |

Currently running on 3 temporary AWS EC2 instances. We'll move to the real servers later.

## Monitoring Stack

- **Prometheus** — Metrics collection (deployed)
- **Node Exporter** — Linux host metrics (deployed)
- **Grafana** — Dashboards and visualization (deployed)
- **Alertmanager** — Alert routing (planned)
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
- Monitoring `monitor-01` and `app-01` through Node Exporter
- All targets verified UP

## Grafana

Running on `monitor-01` using Docker Compose.

- Port: 3000 (localhost only)
- Persistent Docker volume
- Admin credentials configured through `.env`
- Prometheus data source provisioned from YAML
- Imported Node Exporter Full dashboard (ID 1860)
- Created Infrastructure Overview dashboard with 9 panels
- Dashboard JSON provisioned automatically from Git-tracked files

Access Prometheus and Grafana through SSH tunnels.

## Git Workflow

Feature branch → Commit → Push → Pull Request → Review → Merge into main.

## Progress

- [x] Phase 1 — Infrastructure (final checks pending)
- [x] Phase 2 — Prometheus (historical data verification pending)
- [x] Phase 3 — Node Exporter
- [x] Phase 4 — Grafana
- [ ] Phase 5 — Nginx
- [ ] Phase 6 — Telegraf
- [ ] Phase 7 — Loki + Alloy
- [ ] Phase 8 — Alertmanager
- [ ] Phase 9 — Security Hardening
- [ ] Phase 10 — Documentation
