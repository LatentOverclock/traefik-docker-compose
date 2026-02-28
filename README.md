# Traefik Docker Compose

Production-leaning Traefik baseline with automatic HTTPS (Let's Encrypt) and a sample backend (`whoami`).

## What is configured

- Traefik v3
- HTTP (`:80`) → automatic redirect to HTTPS (`:443`)
- Let's Encrypt certificates via ACME HTTP challenge
- Generic catch-all HTTPS route (`HostRegexp`) for quick start
- Easy to replace with explicit host rules for production
- ACME resolver scaffold is included but intentionally not bound to a specific domain by default

## Before first run

1. For production TLS, replace the catch-all router rule in `traefik/dynamic.yml` with explicit host rules.
2. In that router, set `tls.certResolver: letsencrypt`.
3. Set a real ACME email in `traefik/traefik.yml`.
4. Create DNS `A` records at your DNS provider so your domain points to your server.

## Run

```bash
mkdir -p letsencrypt
touch letsencrypt/acme.json
chmod 600 letsencrypt/acme.json

docker compose up -d
```

## Test

After DNS propagates, open your configured domain over HTTPS.

## Notes

- Dashboard API is enabled internally, but not publicly routed in `dynamic.yml`.
- This setup uses file-based routing (works around Docker API provider mismatch on this host).
