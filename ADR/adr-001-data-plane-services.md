# ADR 001: Data Plane Services Architecture

## Status

**Accepted** - February 2026

## Context

The srv-m1m (Apple M1 running Fedora Asahi) serves as the data plane in the infrastructure, hosting all production services. This ADR documents the services architecture.

## Decision

Deploy a comprehensive service stack on srv-m1m using Podman containers and systemd services.

### Services Overview

| Service | Port | Type | Purpose |
|---------|------|------|---------|
| **Forgejo** | 3000 | systemd | Git server with CI/CD |
| **PostgreSQL** | 5432 | systemd | Database with pgvector |
| **Prometheus** | 9090 | systemd | Metrics collection |
| **Grafana** | 3001 | systemd | Visualization |
| **Vaultwarden** | 8222 | Podman | Secrets management |
| **DMR** | 12434 | Podman | AI/LLM (Qwen3) |
| **MCP Server** | 8082 | Podman | AI context protocol |
| **nginx** | 80/443 | Podman | Reverse proxy |
| **Syncthing** | 8384 | systemd | File sync |
| **node_exporter** | 9100 | systemd | System metrics |

### Key Design Decisions

1. **Hybrid Deployment**
   - Some services run as systemd (native)
   - Others run as Podman containers
   - Selection based on management complexity

2. **Tailscale-Only Access**
   - All services accessible only via Tailscale VPN
   - No public port exposure
   - Firewall rules restrict to 100.0.0.0/8

3. **PostgreSQL with pgvector**
   - Standard database for applications
   - pgvector extension for AI/RAG workloads
   - Future-proofing for knowledge bases

4. **Self-Hosted AI**
   - DMR (Docker Model Runner) for local LLM
   - Qwen3 models (8B, 4B, 0.6B)
   - MCP Server for context integration

5. **Infrastructure as Code**
   - All services managed via Ansible
   - 20+ Ansible roles for srv-m1m
   - Reproducible deployment

## Consequences

### Positive
- Complete control over all data
- No vendor lock-in
- Demonstrates enterprise service management
- Self-hosted AI capabilities

### Considerations
- Requires maintenance of multiple services
- Need monitoring for service health
- Backup strategy needed for each service

## References

- srv-m1m GitHub Repo: https://github.com/ch1ch0-foss/labs-srv-m1m
- Ansible Roles: https://github.com/ch1ch0-foss/iac (roles/)
