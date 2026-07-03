# server-config

Shared [Caddy](https://caddyserver.com/) reverse-proxy config for everything hosted on the Hetzner
server. Each site (e.g. [steffan.lol](https://github.com/steffan-harris/steffan.lol)) lives in its
own repo and ships its own container image + `docker-compose.yml`; this repo only owns the Caddy
container and the routing rules that tie site domains to those containers.

## Layout

- `Caddyfile` — one block per site, each reverse-proxying to that site's container by service name
- `docker-compose.yml` — runs the Caddy container on the shared `caddy-net` Docker network that
  site containers also attach to; the network itself is external (created once, owned by no stack)

## Adding a new site

1. Add a block to `Caddyfile` pointing at the new site's container/service name.
2. Make sure the new site's `docker-compose.yml` joins `caddy-net` as an `external` network.
3. Push to `main` — CI copies the config to the server and reloads Caddy.

## First-time server setup

```
docker network create caddy-net   # skip if it already exists
mkdir -p /path/to/server-config && cd /path/to/server-config
# copy Caddyfile + docker-compose.yml here, or let the first CI deploy do it
docker compose up -d
```

Both this stack and the site stacks reference `caddy-net` as external, so the network must exist
before any of them start (the CI deploy also creates it if missing).
