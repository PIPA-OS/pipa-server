# PiPA Server

Self-hosted control plane for running apps and containers at home.

PiPA Server is the console/service that lets you deploy and manage
self-hosted apps and containers from a single web UI. It is distributed as a
container image on GitHub Container Registry (GHCR).

## Quick start

```sh
# pull the image
docker pull ghcr.io/pipa-os/pipa-server:0.1.0

# start it (compose.yaml is in this repo)
docker compose up -d

# open the console
#   http://localhost:8080
```

(podman works identically: `podman pull` / `podman compose up -d`.)

`compose.yaml` in this repository is all you need. The server binary is shipped
inside the container image pulled from GHCR — there is nothing to compile.

## Configuration

| Env / volume        | Purpose                                            |
|---------------------|----------------------------------------------------|
| `8080`              | Web console / OpenAPI listener                      |
| `/run/pipa`         | Host pipa-agent socket (mount `:ro` when present)   |
| `pipa-appstore`     | Persistent imported-apps / appstore data volume    |
| `PIPA_AGENT_SOCKET` | Override the agent socket path (default `/run/pipa/pipa-agent.sock`) |
| `PIPA_SERVER_IMAGE` | Override the image (e.g. to use your own mirror)    |

The agent socket is only needed when `pipa-agent` runs on the host (for example
on PiPA OS). Without it the console still serves, but operations that touch the
host (creating containers/volumes/networks) are unavailable.

## Image & downloads

- Container image: `ghcr.io/pipa-os/pipa-server:0.1.0`
  (packages page: https://github.com/orgs/pipa-os/packages — public)
- This repository contains only the deployment configuration and documentation.
  The server binary is **not open source**.

## License

The files in this repository (compose.yaml, documentation) are released under
the Apache License 2.0 — see [LICENSE](LICENSE). The `pipa-server` binary image
it references is proprietary software distributed via GHCR; its source is not
published here.
