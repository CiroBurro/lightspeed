# Infrastructure Archive

> **Historical infrastructure configurations — no longer active.**
> These files are preserved for reference purposes only.

## Contents

| Directory | Status | Description |
|-----------|--------|-------------|
| `terraform/` | **Decommissioned** | Oracle Cloud Infrastructure (OCI) Terraform configs from v0.2.x era. Replaced by Vultr-based deployment. |
| `fly/` | **Never deployed** | Fly.io configuration explored during v0.3.x planning. Never reached production. |

## Active Infrastructure

The current active deployment uses:
- **Vultr Free Tier** — production proxy nodes
- **Docker** — `infra/docker/` for multi-arch container builds
- **Scripts** — `infra/scripts/` for deployment automation
- **Monitoring** — `infra/monitoring/` for Prometheus/Grafana

## Why Archived

These configurations were preserved rather than deleted because:
1. The Terraform configs contain useful reference patterns for OCI resource provisioning.
2. The Fly.io config may be useful if Fly.io free tier becomes viable.
3. Historical context for architectural decisions (see `wat/state/decisions.md`).

To fully remove this directory, delete `infra/archive/` — no active code depends on it.
