# EC2 Production Setup

**Summary**: AWS EC2 Ubuntu 24 server hosting all Pucho AI services via Dokploy + Traefik.
**Tags**: #infrastructure #aws #ec2 #dokploy #traefik
**Created**: 2026-04-07
**Last Updated**: 2026-04-07

---

## Server Details

- **Provider**: AWS EC2
- **OS**: Ubuntu 24
- **IP**: 13.202.73.154
- **SSH user**: `ubuntu`
- **SSH keys**: `bhaveshbhai.pem` / `bhavesh-server.pem`

## PaaS & Routing

- **Dokploy** — all deployments go through Dokploy (never raw Docker)
- **Traefik** — reverse proxy, auto-discovers Docker services via labels
- **SSL**: Let's Encrypt via Traefik / Certbot
- **Domains**: `*.pucho.ai` — subdomain-based routing (never path-based)

### Traefik Label Pattern

```
traefik.http.routers.<name>.rule=Host('<subdomain>.pucho.ai')
```

## Nginx

Used for static Office add-in serving:
- `excel.office.pucho.ai`
- `ppt.office.pucho.ai`
- `client_max_body_size 50M` required for Office add-in proxies

## VPN

- **ZeroTier** — `192.168.192.x` range for internal access

## Security

- FortiGate firewall
- Wazuh SIEM

## Hard Rules

- Always deploy via Dokploy — never raw `docker run` on EC2
- Never run `apt upgrade` — only `apt install <package>`
- pip installs: always use `--break-system-packages` on Ubuntu 24

## Quick Commands

```bash
# SSH
ssh -i ~/.ssh/bhaveshbhai.pem ubuntu@13.202.73.154

# Nginx reload
sudo nginx -t && sudo nginx -s reload

# SSL renewal
sudo certbot renew --dry-run
```

## Related Notes

- [[dokploy-deployment]]
- [[traefik-routing]]
- [[zerotier-vpn]]
