# Protocols and Integrations Research

**Category:** Agent protocols, tool protocols, registries, and integration standards  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents protocols and integration standards relevant to CTH. Our harness is MCP-first, so MCP is the core protocol. We also evaluated agent-to-agent protocols, registries, gateways, and skill standards, but most are deferred or excluded for v1.

## Repositories / Standards Evaluated

### 1. Model Context Protocol (MCP)
- **Repo:** https://github.com/modelcontextprotocol
- **License:** MIT
- **Primary language:** TypeScript/Python SDKs
- **Maintainers / activity:** Very active, broad adoption
- **Status:** Adopt (core protocol)

#### Key Findings
- Standard protocol for exposing tools/resources to LLM agents.
- Supports stdio and HTTP/SSE transports.
- Cursor supports MCP servers natively via `.cursor/mcp.json`.
- Essential for integrating codebase-memory-mcp, Serena, and our team-workflow MCP.

#### CTH Usage
- All harness tools are MCP servers.
- Use stdio transport for reliability on Windows.
- Our `team-workflow` MCP will wrap other MCP tools.

#### Evidence
- Research sessions and Cursor docs.

---

### 2. A2A (Agent-to-Agent)
- **Repo:** https://github.com/a2aproject/a2a
- **License:** Apache-2.0
- **Primary language:** JSON/OpenAPI
- **Status:** Defer

#### Key Findings
- Protocol for agent-to-agent interoperability.
- Uses Agent Cards for discovery.

#### CTH Usage
- Not needed for v1; may consider if we build multi-agent workflows later.

#### Evidence
- Research session data.

---

### 3. ACP (Agent Communication Protocol)
- **Repo:** Verify URL
- **License:** Verify
- **Status:** Defer

#### Key Findings
- Alternative agent communication protocol.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 4. Agentgateway
- **Repo:** https://github.com/agentgateway/agentgateway
- **License:** Apache-2.0
- **Primary language:** Rust
- **Status:** Study

#### Key Findings
- Centralized data plane for MCP/A2A/LLM traffic.
- Auth, RBAC, rate limiting, telemetry, multi-tenancy.
- Linux Foundation project.

#### CTH Usage
- Borrow centralized governance concepts; not needed as dependency for single-user Cursor harness.

#### Evidence
- Research session data.

---

### 5. MCP Gateway
- **Repo:** https://github.com/microsoft/mcp-gateway
- **License:** MIT
- **Primary language:** Go
- **Status:** Study

#### Key Findings
- Kubernetes-oriented MCP server lifecycle and routing.

#### CTH Usage
- Not needed now; possibly later for remote/multi-user deployments.

#### Evidence
- Research session data.

---

### 6. MCP Registry
- **Repo:** https://github.com/modelcontextprotocol/registry
- **License:** Apache-2.0 / MIT
- **Status:** Optional

#### Key Findings
- Central registry for MCP servers.

#### CTH Usage
- Optional for discovery, but we prefer pinned local MCP configs.

#### Evidence
- Research session data.

---

### 7. Agent Registry
- **Repo:** https://github.com/agentoperations/agent-registry
- **License:** Verify
- **Status:** Optional

#### Key Findings
- Vendor-neutral registry for agents, MCP servers, and skills.
- Uses A2A Agent Cards and OCI concepts.

#### CTH Usage
- Not needed for v1.

#### Evidence
- Research session data.

---

### 8. Agent Skills specification
- **Repo:** https://github.com/agentskills/agentskills
- **License:** Verify
- **Status:** Adopt (format only)

#### Key Findings
- Portable SKILL.md folder format for agent capabilities.
- Structured inputs/outputs and workflow descriptions.
- Works across Claude, Cursor, Codex, etc.

#### CTH Usage
- Use Agent Skills format for role definitions and reusable workflows.
- We may generate SKILL.md from our context bundle.

#### Evidence
- Research session data.

---

### 9. AGENTS.md / Open Knowledge Format
- **Repo:** Various (Cursor, Google)
- **License:** Open / permissive
- **Status:** Adopt (format)

#### Key Findings
- AGENTS.md provides repo-level agent instructions.
- Open Knowledge Format uses Markdown + YAML frontmatter for portable knowledge bundles.
- Preloaded context is more likely to be consumed than retrieval MCPs.

#### CTH Usage
- Use AGENTS.md and PROJECT_CONTEXT.md as our primary context bundle.
- Use YAML frontmatter via gray-matter.

#### Evidence
- Research session data.

---

### 10. MCP Context Forge
- **Repo:** Verify URL
- **License:** Verify
- **Status:** Optional

#### Key Findings
- MCP gateway and federation.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 11. FastMCP
- **Repo:** https://github.com/jlowin/fastmcp
- **License:** MIT
- **Primary language:** Python
- **Status:** Optional

#### Key Findings
- Fast MCP server development in Python.
- Used by Serena and other tools.

#### CTH Usage
- We may use FastMCP if we build the wrapper in Python; otherwise use TypeScript MCP SDK.

#### Evidence
- Research session data.

---

## Integration Strategy

Our integration architecture:

| Component | Protocol / Standard | Implementation |
|-----------|---------------------|----------------|
| Tool access | MCP | `.cursor/mcp.json` |
| Repo graph | MCP stdio | `codebase-memory-mcp` |
| Semantic editing | MCP stdio | `Serena` |
| Workflow orchestration | MCP | custom `team-workflow` MCP |
| Context bundle | Markdown + YAML | `AGENTS.md`, `PROJECT_CONTEXT.md` |
| Role definitions | Agent Skills / Markdown | `.team/roles/*.md` |

All integrations use local stdio MCP where possible for reliability and privacy.

## Decision Summary
- MCP is the core protocol.
- A2A/ACP deferred.
- Agent Skills + AGENTS.md formats adopted.
- Gateways and registries not needed for v1.
- Prefer stdio MCP over HTTP/SSE on Windows.

## Sources
- Research session data.
- Cursor MCP docs, MCP spec, Agent Skills spec.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial protocols and integrations category research |
