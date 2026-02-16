# ADR 003: Self-Hosted AI Platform

## Status

**Accepted** - February 2026

## Context

AI-assisted development increases productivity but relying on external APIs has privacy and cost implications. This ADR documents the decision to build a self-hosted AI platform.

## Decision

Deploy local AI capabilities using DMR (Docker Model Runner) with integration to the development workflow.

### Components

```
┌─────────────────────────────────────────────────────────┐
│                   T480 (Control Plane)                  │
│                                                         │
│  OpenCode ◄─── SSH Tunnel ───► MCP Server (srv-m1m)   │
│                (port 8082)                              │
│                                                         │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│                srv-m1m (Data Plane)                    │
│                                                         │
│  ┌─────────────┐    ┌─────────────┐    ┌────────────┐ │
│  │    DMR      │    │   MCP      │    │  RAG API   │ │
│  │ (Qwen3 8B) │◄──►│  Server    │◄──►│(pgvector)  │ │
│  └─────────────┘    └─────────────┘    └────────────┘ │
│                                                         │
│  Port 12434              Port 8082         Port 11434  │
└─────────────────────────────────────────────────────────┘
```

### Key Decisions

1. **DMR (Docker Model Runner)**
   - OpenAI-compatible API
   - Qwen3 models (8B, 4B, 0.6B)
   - Quantized for efficiency
   - Runs in Podman container

2. **MCP Server**
   - Model Context Protocol implementation
   - Exposes project Markdown as context
   - Runs as systemd service
   - Accessible via Tailscale

3. **OpenCode Integration**
   - AI-powered editor
   - Configured to use DMR endpoint
   - MCP server for project context
   - Local-first, no external APIs

4. **Future: RAG Pipeline**
   - PostgreSQL with pgvector
   - Embedding service for documents
   - Knowledge base from official docs

## Consequences

### Positive
- Complete data privacy (no external API calls)
- No API costs after initial setup
- Demonstrates ML infrastructure skills
- Available offline

### Considerations
- Hardware requirements for models
- Model updates require manual intervention
- Performance vs. cloud APIs

## References

- DMR Documentation
- OpenCode AI Editor
- Model Context Protocol
- Qwen3 Model Family
