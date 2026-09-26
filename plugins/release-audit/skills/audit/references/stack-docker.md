# Stack: Docker / self-hosted (Proxmox, docker compose, reverse proxy)

## Checks
- Images pinned to versions/digests, not `latest`; base images updated; `docker scout cves` or `trivy image` if available
- Containers not running as root where avoidable; no `privileged`; only needed ports published; DB not exposed to internet
- Secrets via env files/secrets not committed to git; `.env` in `.gitignore`
- `restart: unless-stopped` (or always) on all services; verify by rebooting the host/VM in staging and checking all services come back and jobs resume
- Healthchecks defined; reverse proxy (Traefik/Caddy/Nginx) returns correct status when backend down
- Volumes: which hold state; all stateful volumes included in backup
- Log rotation (`logging: max-size/max-file` or journald limits); disk usage and free space alert
- SSL certificates auto-renew; check expiry date; DNS records correct (A/AAAA/CNAME, SPF/DKIM/DMARC for mail)
- Update strategy documented: how to pull new image, migrate, roll back to previous tag
- Restore test: restore latest backup into a separate container/VM, start the app, compare row counts and a sample of records with production, record the result
