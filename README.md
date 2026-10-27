# PiPA Server

Self-hosted control plane for running apps and containers at home.

PiPA Server is the console/service that lets you deploy and manage
self-hosted apps and containers from a single web UI. It is distributed as a
container image on GitHub Container Registry (GHCR).

## Quick start

```sh
# pull the image
docker pull ghcr.io/pipa-os/pipa-server:0.1.4

# start it (compose.yaml is in this repo)
docker compose up -d

# open the console
#   http://localhost:8080
```

(podman works identically: `podman pull` / `podman compose up -d`.)

`compose.yaml` in this repository is all you need. The server binary is shipped
inside the container image pulled from GHCR — there is nothing to compile.

## Container runtime (required)

PiPA Server talks to the **host** container runtime over its Docker-compatible
Unix socket and manages containers on that host directly. The socket **must be
bind-mounted into the container**, otherwise the server fails with
`容器运行时不可用`.

`compose.yaml` mounts `/var/run/docker.sock` by default. If the host uses
**podman (rootful)** instead, swap that line for:

```yaml
      - /run/podman/podman.sock:/run/podman/podman.sock
```

(Do not use the `containerd` backend — it is intentionally unsupported:
containerd does not provide name resolution, log storage, or restart policies,
so driving it would mean reimplementing those. The probe reports it as
unsupported.)

## Configuration

| Env / volume        | Purpose                                            |
|---------------------|----------------------------------------------------|
| `8080`              | Web console / OpenAPI listener                      |
| `/var/run/docker.sock` | Host container runtime socket (mounted by default)   |
| `/run/podman/podman.sock` | Podman runtime socket (use instead of docker.sock)   |
| `/run/pipa`         | Host pipa-agent socket (mount `:ro` when present)   |
| `pipa-appstore`     | Persistent imported-apps / appstore data volume    |
| `PIPA_RUNTIME`      | `auto` (default) \| `podman` \| `docker`            |
| `PIPA_DOCKER_SOCKET` | Override the docker socket path inside the container |
| `PIPA_PODMAN_SOCKET` | Override the podman socket path inside the container |
| `PIPA_AGENT_SOCKET` | Override the agent socket path (default `/run/pipa/pipa-agent.sock`) |
| `PIPA_SERVER_IMAGE` | Override the image (e.g. to use your own mirror)    |

The agent socket is only needed when `pipa-agent` runs on the host (for example
on PiPA OS). Without it the console still serves, but operations that touch the
host (creating containers/volumes/networks) are unavailable.

## Image & downloads

- Container image: `ghcr.io/pipa-os/pipa-server:0.1.4`
  (packages page: https://github.com/orgs/pipa-os/packages — public)
- This repository contains only the deployment configuration and documentation.
  The server binary is **not open source**.

## License

The files in this repository (compose.yaml, documentation) are released under
the Apache License 2.0 — see [LICENSE](LICENSE). The `pipa-server` binary image
it references is proprietary software distributed via GHCR; its source is not
published here.
