# homelab

Docker Compose setup for my home server (Raspberry Pi). Services are exposed to the internet through a Cloudflare tunnel (`cloudflared`) that points at nginx.

## Services

| Service | Purpose | Notes |
|---------|---------|-------|
| **nginx** | Reverse proxy | Listens on port 80; cloudflared ingress rules point here. Config in `nginx/conf.d`, static files in `nginx/html`. Also fails over for Jenkins. |
| **gitea** | Self-hosted Git (`git.theshaeffers.com`) | SQLite, registration disabled, Actions enabled. Data in `/home/pi/gitea-data`. SSH on port 222 (not proxied). |
| **n8n** | Workflow automation | Configured via `.env`; `./local-files` is mounted at `/local-files`. |

A Gitea Actions runner is stubbed out (commented) at the bottom of `docker-compose.yml`.

## Setup

1. Create a `.env` with the n8n variables: `N8N_HOST`, `N8N_PROTOCOL`, `N8N_PORT`, `WEBHOOK_URL`, `N8N_EDITOR_BASE_URL`, `GENERIC_TIMEZONE`, `N8N_LOG_LEVEL`, `N8N_SECURE_COOKIE`.
2. The `n8n_data` volume is declared `external`, so it must exist first. Create it with `docker volume create n8n_data`, or copy in existing data when migrating.
3. Start everything:

   ```sh
   docker compose up -d
   ```

## Networks

- `proxy_network`: shared by nginx, gitea and n8n.
- `n8n_network`: n8n only.
