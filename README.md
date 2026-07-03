# server-config

Shared [Caddy](https://caddyserver.com/) reverse-proxy config for everything hosted on the Hetzner
server. Each site (e.g. [steffan.lol](https://github.com/Steffan-Harris/steffan.lol)) lives in its
own repo and ships its own container image + `docker-compose.yml`; this repo only owns the Caddy
container and the routing rules that tie site domains to those containers.

## Layout

- `Caddyfile` — one block per site, each reverse-proxying to that site's container by service name
- `docker-compose.yml` — runs the Caddy container and creates the `caddy-net` Docker network that
  site containers attach to

## Adding a new site

1. Add a block to `Caddyfile` pointing at the new site's container/service name.
2. Make sure the new site's `docker-compose.yml` joins `caddy-net` as an `external` network.
3. Push to `main` — CI copies the config to the server and reloads Caddy.

## First-time server setup

```
mkdir -p /path/to/server-config && cd /path/to/server-config
# copy Caddyfile + docker-compose.yml here, or let the first CI deploy do it
docker compose up -d
```

Site stacks depend on `caddy-net` already existing, so this stack should be brought up before any
site stacks that reference it.
