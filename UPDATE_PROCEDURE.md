# Update Procedure — Observability Lab

## 1. Before Updating
- Create a feature branch in Git.
- Record current versions and verify that all services are healthy.
- Back up configuration files and persistent Docker volumes.
- Never commit passwords, tokens, certificates' private keys, or `.env` files.

## 2. Updating Docker Services
Applies to Prometheus, Grafana, Loki, and Alertmanager.

1. Change the pinned image version in `monitor-01/docker-compose.yml`.
2. Validate configuration:
   `docker compose config -q`
3. Pull the new image:
   `docker compose pull <service>`
4. Recreate only the updated service:
   `docker compose up -d --no-deps <service>`
5. Check:
   `docker compose ps`
   `docker compose logs --since 5m <service>`

## 3. Updating Systemd Services
Applies to Node Exporter, NGINX Exporter, Telegraf, and Alloy.

1. Back up the existing binary and configuration.
2. Install the tested new version.
3. Validate configuration where supported.
4. Restart the service with `systemctl restart`.
5. Verify with `systemctl status` and `journalctl`.

## 4. Post-Update Verification
- Check that all Prometheus targets are UP.
- Verify Grafana dashboards and data sources.
- Verify Loki log ingestion.
- Test an alert through Alertmanager to MoovingON AI.
- Confirm alert recovery and check for new errors.
- Verify TLS certificates and authentication.

## 5. Rollback
- Restore the previous pinned image version or binary.
- Restore backed-up configuration and persistent data if required.
- Recreate or restart the affected service.
- Repeat health checks and alert delivery tests.
- Document the failure and its resolution.

## 6. Git Workflow
Feature branch → Commit → Push → Pull Request → Review → Merge.

Never run `docker compose down -v` during routine updates because it removes persistent volumes.
