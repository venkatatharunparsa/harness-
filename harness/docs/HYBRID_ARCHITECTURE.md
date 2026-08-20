# Hybrid Architecture: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Purpose
This document consolidates our hybrid best-of-breed architecture. It maps each selected open-source repository to a specific CTH component, explains what we borrow/adopt/integrate, and defines the final component stack. The goal is to extract the strongest ideas from multiple repositories and combine them into a cohesive, production-grade harness without becoming dependent on any single project.

## 2. Hybrid Philosophy
CTH is not a monolith. It is a modular harness composed of layered components. Each component is selected based on evidence, license safety, and fit. We adopt direct dependencies where they are mature and MIT/Apache-safe. We borrow patterns and workflows from others and re-implement them ourselves. We never fork noncommercial or AGPL code into a proprietary SaaS.

## 3. Repository-to-Component Map

| Repository | Role in CTH | What We Take | Adoption Mode |
|---|---|---|---|
| Serena | Exact LSP-grade code intelligence | Symbol lookup, references, rename, diagnostics, Dart/Flutter semantic safety | Adopt as MCP server |
| codebase-memory-mcp | Persistent structural graph + impact analysis | SQLite graph, detect_changes, trace_call_path, monorepo indexing | Adopt as MCP server; wrap with our own MCP |
| OpenHands | Reference architecture | Event-sourced state, risk-based confirmation, secret registry, condensation | Study patterns, implement ourselves |
| GitNexus | Confidence-ranked impact + process tracing | Concept of depth-grouped blast radius, confidence scoring, execution flows | Rebuild externally on top of codebase-memory-mcp |
| dependency-cruiser | Architecture boundary enforcement | Circular dependency detection, module rules | Adopt as CLI / fitness function |
| eslint-plugin-boundaries | IDE-native architecture boundaries | Enforce import rules in TypeScript/Next.js | Adopt for frontend/Node |
| Semgrep | Security gate extension | Custom static analysis rules for FastAPI/Node/Flutter | Adopt as scanner in Sentinel v2 |
| gitleaks + trivy + opengrep | Existing Sentinel core | Secrets, IaC, dependencies, SAST | Keep existing |
| pino / structlog | JSONL audit logging | Fast structured logging | Use in team-workflow MCP |
| gray-matter | Context bundle parsing | YAML frontmatter parsing for AGENTS.md / PROJECT_CONTEXT.md | Use in context generator |
| chokidar | File watcher | Watch `.team/state.json` and context files | Use in watcher service |
| DeepSeek Harness | Plugin-first architecture | Append-only session log, resume/replay, modular plugins | Study patterns |
| Temporal | Durable workflow patterns | Deterministic state, retries, approvals | Borrow concepts, not dependency |
| TencentDB Agent Memory | Layered memory | L0-L3 context pyramid, agent loadout | Borrow pattern for context bundle |

## 4. How We Combine the Hybrid

### Code Intelligence Layer
- Primary: `codebase-memory-mcp` for persistent repo graph, Git-diff impact, call paths, monorepo discovery.
- Precision: `Serena` for LSP-grade symbol operations, especially Dart/Flutter and TypeScript.
- Enrichment: our custom `team-workflow` MCP wraps both, adds confidence scoring, process aggregation, and token capping.

### Enforcement Layer
- `preToolUse` + `beforeShellExecution` hooks block protected file and shell actions.
- System Gate v2: schema validation + `dependency-cruiser` fitness functions.
- Sentinel v2: core scanners + `Semgrep` + supply chain + dynamic/runtime stages.
- Production gates: reliability, performance, observability, deployment, scalability, cost.

### State & Audit Layer
- Append-only JSONL event log inspired by OpenHands and DeepSeek Harness.
- Snapshot recovery from `.team/state.json`.
- Structured logs via `pino` or `structlog`.
- Context bundle parsed with `gray-matter`.

### Context & Memory Layer
- Git-backed `AGENTS.md` and `PROJECT_CONTEXT.md` with YAML frontmatter.
- Layered context: summary → decisions → detailed artifacts.
- No separate retrieval MCP unless field evidence proves usage.

## 5. Final CTH Component Stack

| Layer | Primary | Secondary / Complement |
|---|---|---|
| Exact code intelligence | Serena | tree-sitter |
| Repo graph memory | codebase-memory-mcp | custom wrapper MCP |
| Architecture gate | System Gate + dependency-cruiser | eslint-plugin-boundaries |
| Security gate | Sentinel + Semgrep | gitleaks, trivy, osv-scanner |
| Logging / audit | pino / structlog | custom JSONL wrapper |
| Context parsing | gray-matter | custom YAML parser |
| File watcher | chokidar | Python watchdog |
| Workflow state | Custom JSON state ledger | xstate later if needed |
| MCP integration | MCP TypeScript/Python SDK | — |

## 6. License Verification
- Serena: MIT — verified from earlier research.
- codebase-memory-mcp: MIT — verified.
- GitNexus: PolyForm Noncommercial — excluded as dependency; use ideas only.
- dependency-cruiser: MIT.
- Semgrep: LGPL 2.1 — acceptable as external CLI process; not embedded.
- pino/structlog/gray-matter/chokidar: MIT/Apache/ISC — safe.
- OpenHands: MIT — study only.

## 7. Validation Before Integration
For each adopted dependency:
- Confirm current license from repository LICENSE file.
- Check last commit and release cadence.
- Test inside Cursor with minimal project.
- Verify language support for Node/FastAPI/Flutter.
- Measure token output, latency, and resource usage.
- Document failure modes and rollback path.

## 8. Open Questions
- Is Semgrep redundant with opengrep, or do we need both? Research needed.
- Does Serena's Dart LSP integration work smoothly in Cursor? Validate.
- How to best combine `codebase-memory-mcp` and Serena outputs in one wrapper?
- What is the exact precision/recall of our combined impact analysis? Measure in dogfood.

## 9. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial hybrid architecture draft |
