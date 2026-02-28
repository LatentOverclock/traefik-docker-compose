# Traefik Docker Compose

Generic Traefik reverse-proxy base for multi-app deployments.

## What this base does

- Runs Traefik v3
- Redirects HTTP (`:80`) to HTTPS (`:443`)
- Uses Let's Encrypt ACME (HTTP challenge)
- Reads app routes from **Docker labels**
- Uses shared external Docker network: `edge-proxy`

## One-time setup

```bash
docker network create edge-proxy || true
mkdir -p letsencrypt
touch letsencrypt/acme.json
chmod 600 letsencrypt/acme.json
```

Set your ACME email in `traefik/traefik.yml`.

Create `.env` from `.env.example` to define dashboard host + basic-auth users.

## Run

```bash
cp .env.example .env
# edit .env values

docker compose up -d
```

## How apps integrate (from their own repo)

1. Join external network `edge-proxy`
2. Add Traefik labels on the app service, for example:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.docker.network=edge-proxy"
  - "traefik.http.routers.myapp.rule=Host(`example.com`)"
  - "traefik.http.routers.myapp.entrypoints=websecure"
  - "traefik.http.routers.myapp.tls.certresolver=letsencrypt"
  - "traefik.http.services.myapp.loadbalancer.server.port=80"
```

## Notes

- Keep this repo domain-agnostic.
- Domain-specific routing should live in each app's compose labels.
