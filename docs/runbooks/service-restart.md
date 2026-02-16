# Service Restart Runbook

## Overview

This runbook describes how to restart services on srv-m1m.

## Checking Service Status

### List All Services

```bash
ssh srv-m1m 'systemctl list-units --type=service --state=running'
```

### Check Specific Service

```bash
# Forgejo
ssh srv-m1m 'systemctl status forgejo'

# PostgreSQL
ssh srv-m1m 'systemctl status postgresql'

# Prometheus
ssh srv-m1m 'systemctl status prometheus'
```

## Restarting Services

### Single Service

```bash
ssh srv-m1m 'sudo systemctl restart <service-name>'

# Example
ssh srv-m1m 'sudo systemctl restart forgejo'
```

### Containerized Services

```bash
# DMR (Podman)
ssh srv-m1m 'podman restart dmr'

# MCP Server
ssh srv-m1m 'podman restart mcp-server'

# Vaultwarden
ssh srv-m1m 'podman restart vaultwarden'
```

## Viewing Logs

### Journalctl

```bash
# Last 50 lines
ssh srv-m1m 'journalctl -u forgejo -n 50'

# Follow in real-time
ssh srv-m1m 'journalctl -u forgejo -f'

# Since specific time
ssh srv-m1m 'journalctl -u forgejo --since "1 hour ago"'
```

### Podman Logs

```bash
# Container logs
ssh srv-m1m 'podman logs dmr'

# Follow mode
ssh srv-m1m 'podman logs -f dmr'
```

## Health Checks

### After Restart

```bash
# Verify service is running
ssh srv-m1m 'systemctl is-active forgejo'

# Check port is listening
ssh srv-m1m 'ss -tlnp | grep 3000'

# Test endpoint
curl -I http://srv-m1m:3000
```

## Common Issues

### Service Won't Start

1. Check logs for errors
2. Verify configuration files
3. Check port availability
4. Verify disk space

### Service Starts But Crashes

1. Check journal logs
2. Verify dependencies
3. Check resource limits
