# Troubleshooting Guide

This guide covers common issues and their solutions for the srv-m1m data plane.

## Service Issues

### Service Won't Start

**Diagnosis**:
```bash
# Check service status
systemctl status <service>

# View logs
journalctl -u <service> -n 100
```

**Common Causes**:
1. Port already in use
2. Configuration error
3. Missing dependencies
4. Disk space full

**Solutions**:
1. Check port: `ss -tlnp | grep <port>`
2. Validate config files
3. Check dependencies: `systemctl list-dependencies <service>`
4. Check disk: `df -h`

### Service Starts Then Crashes

**Diagnosis**:
```bash
# Follow logs in real-time
journalctl -u <service> -f

# Check for OOM kills
dmesg | grep -i oom
```

**Solutions**:
1. Check memory usage
2. Review service limits
3. Check for configuration errors
3. Verify disk space

## Network Issues

### Cannot Connect to Service

**Diagnosis**:
```bash
# Check service is listening
ss -tlnp | grep <port>

# Check firewall
firewall-cmd --list-all

# Test connectivity
curl -v http://localhost:<port>
```

**Solutions**:
1. Verify service is running
2. Check firewall rules allow port
3. Verify binding address (should be 0.0.0.0 or Tailscale IP)
4. Check Tailscale connectivity

### Tailscale Connection Issues

**Diagnosis**:
```bash
tailscale status
tailscale ping <peer>
```

**Solutions**:
1. Restart Tailscale: `sudo systemctl restart tailscaled`
2. Check network connectivity
3. Verify Tailscale ACLs

## Database Issues

### PostgreSQL Connection Failed

**Diagnosis**:
```bash
# Check PostgreSQL status
systemctl status postgresql

# Check logs
journalctl -u postgresql -n 50

# Test connection
psql -U postgres -h localhost
```

**Solutions**:
1. Verify PostgreSQL is running
2. Check pg_hba.conf for authentication
3. Verify database exists
4. Check disk space

### High Database Load

**Diagnosis**:
```bash
# Check active connections
psql -U postgres -c "SELECT count(*) FROM pg_stat_activity;"

# Check slow queries
psql -U postgres -c "SELECT * FROM pg_stat_activity WHERE state = 'active';"
```

**Solutions**:
1. Kill idle connections
2. Optimize queries
3. Add indexes
4. Consider connection pooling

## Container Issues (Podman)

### Container Won't Start

**Diagnosis**:
```bash
# Check container status
podman ps -a

# View container logs
podman logs <container>

# Check podman events
podman events --since 5m
```

**Solutions**:
1. Check port conflicts
2. Verify volume mounts
3. Check resource limits
4. Review container configuration

### DMR (AI) Issues

**Diagnosis**:
```bash
# Check DMR container
podman logs dmr

# Check GPU availability
podman exec dmr nvidia-smi  # if NVIDIA
```

**Solutions**:
1. Verify container is running
2. Check model files exist
3. Verify port 12434 is accessible
4. Check memory availability

## Monitoring Issues

### Prometheus Target Down

**Diagnosis**:
```bash
# Check target endpoint
curl http://localhost:9100/metrics

# Check Prometheus targets
curl http://localhost:9090/api/v1/targets | jq
```

**Solutions**:
1. Verify node_exporter is running
2. Check firewall rules
3. Verify scrape interval
4. Check authentication

### Grafana Dashboard Not Loading

**Diagnosis**:
```bash
# Check Grafana status
systemctl status grafana-server

# Check logs
journalctl -u grafana-server -n 50
```

**Solutions**:
1. Verify data source is configured
2. Check Prometheus is accessible
3. Review dashboard queries
4. Check browser console

## Recovery Procedures

### Restart All Services

```bash
# Stop all services
for svc in forgejo prometheus grafana-server postgresql; do
    sudo systemctl stop $svc
done

# Start all services
for svc in postgresql prometheus grafana-server forgejo; do
    sudo systemctl start $svc
done
```

### Complete Server Rebuild

```bash
# From T480 control plane
cd ~/iac
ansible-playbook playbooks/srv-m1m-full.yml
```

### Database Recovery

```bash
# Stop PostgreSQL
sudo systemctl stop postgresql

# Restore from backup
pg_restore -U postgres -d <database> /path/to/backup.dump

# Start PostgreSQL
sudo systemctl start postgresql
```
