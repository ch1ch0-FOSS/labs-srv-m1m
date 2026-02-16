# ADR 002: Zero-Trust Network with Tailscale

## Status

**Accepted** - February 2026

## Context

Traditional network security assumes everything inside the perimeter is trusted. This doesn't work for a distributed infrastructure. This ADR documents the decision to use Tailscale for zero-trust networking.

## Decision

Use Tailscale as the primary network connectivity solution with all services accessible only via the VPN.

### Architecture

```
                    ┌─────────────────────┐
                    │   Tailscale        │
                    │   Network          │
                    │   100.0.0.0/8      │
                    └─────────┬───────────┘
                              │
              ┌───────────────┼───────────────┐
              │               │               │
              ▼               ▼               ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │  T480    │   │ srv-m1m  │   │ External │
        │ Control  │   │  Data    │   │   Host   │
        │  Plane   │◄─►│  Plane   │   │ (tailscale)│
        └──────────┘   └──────────┘   └──────────┘
```

### Key Decisions

1. **VPN-First Architecture**
   - All services bind to Tailscale interface only
   - No port forwarding on home router
   - Services invisible to public internet

2. **Network Segmentation**
   - Control plane: 100.127.184.81 (T480)
   - Data plane: 100.104.206.96 (srv-m1m)
   - Subnet router for home network access

3. **Firewall Rules**
   - Default: deny all incoming
   - Allow from Tailscale network (100.0.0.0/8)
   - Service-specific ports only

4. **Access Control**
   - Tailnet ACLs for device restrictions
   - Two-factor authentication via phone
   - Key-based device approval

## Consequences

### Positive
- Services completely hidden from internet
- Encrypted all the way
- Easy remote access from anywhere
- No NAT traversal issues
- Demonstrates zero-trust principles

### Considerations
- Requires Tailscale client on all devices
- Dependent on Tailscale availability
- Some services may need web UI exposure (future)

## References

- Tailscale Documentation
- Zero Trust Security Model
- CISA Zero Trust Maturity Model
