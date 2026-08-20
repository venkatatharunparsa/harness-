# Architecture: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Overview
The Constellation Team Harness (CTH) is a modular, IDE-native layer that wraps a coding agent and enforces a disciplined software engineering process. It runs primarily inside Cursor, with future adapters for Claude Code, Codex, and Antigravity.

The architecture separates workflow control, code intelligence, context management, enforcement, and observability so that no single component is monolithic and each can be replaced or removed based on field evidence.

## 2. Design Goals and Principles
- **Orchestration outside the model:** critical state and transitions live in files and validators, not in the agent’s reasoning.
- **Evidence-based gates:** PASS/FAIL/ERROR only from external tools.
- **Versioned context:** every context snapshot records the Git commit and graph index version.
- **Human approval:** the human is the final approver for every phase transition.
- **Modularity:** tools and components can be swapped as new evidence appears.
- **Auditability:** append-only logs capture every meaningful action.

## 3. High-Level Architecture Diagram
```
┌──────────────────────────── Cursor IDE ─────────────────────────────┐
│                                                                     │
│   Human (approver)  ←───── Commands / prompts ─────→  Coding Agent   │
│         ▲                                                │          │
│         │                                                │          │
│         ▼                                                ▼          │
│   Team Commands (.cursor/commands)          Cursor Rules (.cursor/rules)│
│         │                                                │          │
│         ▼                                                ▼          │
│   Team Workflow MCP  ──────►  Code Intelligence MCP(s)   │          │
│         │                         │                       │          │
│         │                         │                       │          │
│         ▼                         ▼                       │          │
│   State Ledger (.team/state.json) ──► Audit Log (.team/audit.jsonl)│
│   Context Bundle (AGENTS.md, PROJECT_CONTEXT.md)                    │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
              │                                │
              ▼                                ▼
   System Gate (architecture)         Sentinel (security)
   .system-gate/architecture.json     .sentinel/run-ledger.json
```

## 4. Core Components

### 4.1 Team Workflow Engine
- Manages phases: Requirements → Architecture → Implementation → Security Review → QA → Release.
- Defines roles: Product Analyst, Architect, Backend Developer, Frontend Developer, Security Reviewer, QA Engineer, Release Manager.
- Maintains `.team/state.json` as the single source of truth.
- Exposes MCP tools to query state, request approval, record artifacts, and advance phases after validation.
- Enforces that transitions require human approval.

### 4.2 Code Intelligence Layer
- Primary graph engine: `codebase-memory-mcp`.
  - Provides `search_graph`, `trace_call_path`, `detect_changes`, `get_architecture`, `get_code_snippet`, and `manage_adr`.
  - Maintains persistent SQLite graph with incremental indexing.
- Optional LSP layer: `Serena`.
  - Provides semantic symbol lookup, references, rename, body replacement, diagnostics.
  - Activated later for precise refactoring tasks.
- Both are exposed as MCP servers and referenced by Cursor rules.

### 4.3 Context & Memory System
- Generates:
  - `AGENTS.md` — always-preloaded current state, phase, role, decisions, constraints.
  - `PROJECT_CONTEXT.md` — detailed architecture summary, decisions, notChecked list, evidence links.
- Context bundle is Git-versioned and updated after each phase.
- Version alignment: each context file records Git commit/hash and graph index version.
- No separate retrieval MCP unless evidence shows need.

### 4.4 Enforcement Layer
- Integrates existing gates:
  - System Gate for architecture readiness (`architecture_readiness`).
  - Sentinel for security ship brake (`ship_readiness`).
- Cursor rules force agent to call code intelligence before edits and gates at appropriate triggers.
- Git hooks (if available) can enforce pre-commit/pre-push checks.
- No bypass flags. Overrides require config edits.

### 4.5 Logging & Observability
- Append-only logs in `.team/logs/` as JSONL.
- Each log record contains:
  - timestamp, event type, actor (agent/human), phase, git commit/hash, tool, result, details.
- Event types include:
  - `phase_transition`
  - `approval`
  - `gate_call`
  - `code_intel_query`
  - `agent_edit`
  - `tool_call`
  - `context_injection`
- Logs are queryable for post-mortem and research.

### 4.6 Host Integration Layer
- Cursor-specific:
  - `.cursor/mcp.json` for MCP servers.
  - `.cursor/rules/*.mdc` for behavioral rules.
  - `.cursor/commands/team-*.md` for human commands.
- Other hosts later:
  - Claude Code: `CLAUDE.md` + MCP config.
  - Codex: MCP config.
  - Antigravity: host-specific hooks and MCP config.

## 5. Data Flow / Key Workflows

### 5.1 Phase Transition Flow
```
Human approves phase
        ↓
Team Workflow MCP updates state to "completed"
        ↓
Team Workflow MCP validates acceptance criteria (artifacts + gates)
        ↓
Team Workflow MCP prompts human for approval to next phase
        ↓
Human approves next phase
        ↓
State updates, context bundle regenerated, audit log written
```

### 5.2 Agent Edit Flow
```
Agent receives task
        ↓
Cursor rule triggers: query code intelligence
        ↓
Agent calls search_graph / trace_call_path
        ↓
Agent lists planned files and symbols
        ↓
Human/agent proceeds with edit
        ↓
After edit, agent calls detect_changes
        ↓
Affected files and tests identified
        ↓
Relevant tests/checks run
        ↓
Audit log records edit and results
```

### 5.3 Context Refresh Flow
```
Phase completes
        ↓
Team Workflow MCP collects artifacts and decisions
        ↓
Generates AGENTS.md + PROJECT_CONTEXT.md
        ↓
Records Git commit/hash and graph index version
        ↓
Commits updated context bundle
```

## 6. State and Audit Models

### 6.1 State Ledger (`.team/state.json`)
```
{
  "current_phase": "requirements",
  "phase_status": "in_progress",
  "completed_phases": [],
  "artifacts": {
    "requirements.md": { "status": "pending_review", "path": "docs/artifacts/requirements.md" }
  },
  "approvals": [
    { "phase": "requirements", "decision": "approve", "timestamp": "...", "comment": "" }
  ],
  "context_bundle_commit": "abc123",
  "graph_index_version": "v1.2.3"
}
```

### 6.2 Audit Log (`.team/audit.jsonl`)
```
{"ts":"2026-08-20T10:00:00Z","event":"approval","actor":"human","phase":"requirements","decision":"approve","commit":"abc123"}
{"ts":"2026-08-20T10:05:00Z","event":"gate_call","tool":"architecture_readiness","result":"PASS","evidence":"...","commit":"abc123"}
```

## 7. Integration Points
- **MCP servers:** team-workflow, codebase-memory-mcp, system-gate, sentinel, (optional) serena.
- **Filesystem paths:** `.team/`, `.cursor/`, `.system-gate/`, `.sentinel/`.
- **Git:** state ledger, context bundle, logs (except sensitive or transient files), artifacts.

## 8. Component Interactions
- Team Workflow MCP orchestrates by calling:
  - Code Intelligence MCP for impact analysis before/after edits.
  - System Gate MCP for architecture validation.
  - Sentinel MCP for security validation.
- Cursor rules guide the agent to call Team Workflow MCP at phase triggers.
- Human uses commands to approve/reject/status.

## 9. Technology Choices (based on research)
| Component | Choice | Rationale |
|---|---|---|
| Code graph | `codebase-memory-mcp` | MIT, local, persistent SQLite graph, detect_changes, trace_call_path, good TypeScript/Python |
| Semantic editing | `Serena` | MIT, LSP-backed precise symbols, references, rename, diagnostics |
| Architecture gate | System Gate (existing) | Evidence-based architecture readiness |
| Security gate | Sentinel (existing) | Wraps scanners, produces ship readiness |
| Context | Git-backed Markdown | Preloaded, version-controlled, no retrieval MCP |
| Logging | JSONL files | Append-only, queryable, research-friendly |
| Primary host | Cursor | Native MCP/rules/commands, familiar |

## 10. Versioning and Staleness
- State ledger records current Git commit/hash.
- Context bundle records the commit and graph index version at generation time.
- Before high-risk edits, Team Workflow MCP checks if context is stale:
  - If current commit != context commit, prompt re-index and update context.
  - If code graph index is stale, call re-index or mark as stale.

## 11. Failure Modes and Recovery
- **Cursor crash mid-phase:** state ledger is file-based, resume from last completed step.
- **MCP server unavailable:** Team Workflow MCP returns ERROR; no PASS; human can diagnose.
- **Stale graph:** context version mismatch triggers re-index; high-risk edits blocked until fresh.
- **Agent ignores rules:** human sees missing gate/artifact and rejects.
- **Tool license change:** we swap component; modular design allows it.

## 12. Open Questions
- Exact team-workflow MCP language/stack (TypeScript recommended).
- How to handle monorepo multi-root graph indexing.
- Which Cursor hooks are truly enforceable vs soft.
- How much graph version history to retain.
- Whether Serena should be active by default or on-demand.
- How to integrate external scanners with Cursor’s shell/hook limitations.

## 13. Traceability to Requirements
| Architecture component | Fulfills FRs |
|------------------------|--------------|
| Team Workflow Engine | FR-01..FR-07 |
| Code Intelligence Layer | FR-08..FR-13 |
| Context & Memory System | FR-14..FR-17 |
| Enforcement Layer | FR-18..FR-21 |
| Logging & Observability | FR-22..FR-24 |
| Host Integration | FR-25..FR-27 |

## 14. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
