# srv-m1m Runbooks Overview

This directory contains operational procedures for the srv-m1m data plane server.

## Available Runbooks

### Service Management

- **[Service Deployment](service-deployment.md)** - Deploying services with Ansible
- **[Service Restart](service-restart.md)** - Restarting individual services
- **[Service Logs](service-logs.md)** - Viewing service logs with journalctl

### Infrastructure

- **[PostgreSQL Management](postgresql-management.md)** - Database administration
- **[Vaultwarden Backup](vaultwarden-backup.md)** - Secrets backup procedures
- **[Forgejo Administration](forgejo-administration.md)** - Git server management

### Monitoring

- **[Prometheus Configuration](prometheus-configuration.md)** - Metrics setup
- **[Grafana Dashboards](grafana-dashboards.md)** - Dashboard management
- **[Alert Management](alert-management.md)** - Alert configuration

### Security

- **[Firewall Rules](firewall-rules.md)** - firewalld configuration
- **[Tailscale Management](tailscale-management.md)** - VPN operations
- **[Security Auditing](security-auditing.md)** - Security checks

### Backup & Recovery

- **[Backup Strategy](backup-strategy.md)** - Comprehensive backup procedures
- **[Disaster Recovery](disaster-recovery.md)** - Recovery procedures
- **[Database Backup](database-backup.md)** - PostgreSQL backups

## Quick Reference

### Common Operations

```bash
# Check all services
ssh srv-m1m 'systemctl list-units --type=service --state=running'

# View service logs
ssh srv-m1m 'journalctl -u forgejo -n 50'

# Restart a service
ssh srv-m1m 'sudo systemctl restart prometheus'

# Check ports
ssh srv-m1m 'ss -tlnp | grep LISTEN'
```

### Service Ports

| Service | Port | Type |
|---------|------|------|
| Forgejo | 3000 | Git + CI/CD |
| PostgreSQL | 5432 | Database |
| Prometheus | 9090 | Metrics |
| Grafana | 3001 | Dashboards |
| Vaultwarden | 8222 | Secrets |
| DMR | 12434 | AI/LLM |
| MCP Server | 8082 | AI context |
| nginx | 80/443 | Proxy |
| Syncthing | 8384 | Sync |
| node_exporter | 9100 | Metrics |

## Maintenance Schedule

- **Daily**: Check monitoring, review alerts
- **Weekly**: Backup verification, log review
- **Monthly**: Package updates, security scans
- **Quarterly**: Disaster recovery testing
