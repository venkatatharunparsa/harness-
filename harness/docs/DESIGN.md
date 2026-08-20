# Design: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Purpose
This document provides the detailed technical design for CTH. It defines state models, role definitions, MCP tool interfaces, context bundle structure, logging formats, error semantics, and operational flows. It must remain consistent with `ARCHITECTURE.md` and traceable to `REQUIREMENTS.md`.

## 2. Directory and File Layout

```
harness_ide/
├── .cursor/
│   ├── mcp.json
│   ├── commands/
│   │   ├── team-status.md
│   │   ├── team-approve.md
│   │   ├── team-reject.md
│   │   └── team-next.md
│   └── rules/
│       ├── team-workflow.mdc
│       └── codebase-context.mdc
├── .team/
│   ├── state.json
│   ├── audit.jsonl
│   ├── logs/
│   │   ├── events.jsonl
│   │   └── summaries/
│   └── contexts/
│       ├── AGENTS.md
│       └── PROJECT_CONTEXT.md
├── src/
│   ├── mcp/
│   │   ├── server.ts
│   │   ├── tools/
│   │   │   ├── state.tools.ts
│   │   │   ├── approval.tools.ts
│   │   │   ├── artifact.tools.ts
│   │   │   └── validate.tools.ts
│   │   └── index.ts
│   ├── core/
│   │   ├── workflow.ts
│   │   ├── roles.ts
│   │   ├── state.ts
│   │   ├── context.ts
│   │   └── logger.ts
│   └── utils/
├── tests/
│   ├── unit/
│   └── integration/
└── package.json
```

## 3. Team Workflow Design

### 3.1 Phases
| Phase | Goal | Outputs | Acceptance Criteria |
|-------|------|---------|---------------------|
| Requirements | Understand product intent | `requirements.md`, clarifications | Human approval |
| Architecture | Design system architecture | `.system-gate/architecture.json`, architecture doc | System Gate PASS + human approval |
| Implementation | Build backend and frontend | Source code, tests, context | Sentinel check PASS, tests pass, human approval |
| Security Review | Validate security posture | Security report, Sentinel PASS | `ship_readiness` PASS + human approval |
| QA | Validate runtime and quality | Test reports, smoke evidence | Runtime gate + smoke + human approval |
| Release | Prepare production release | Release notes, deployment plan | Final human approval |

### 3.2 Roles
| Role | Responsibilities | Tools Allowed | Forbidden |
|------|------------------|---------------|-----------|
| Product Analyst | Clarify requirements, create PRD artifacts | file read/write, web research optional | code editing, deployment |
| Architect | Design system architecture, create architecture record | System Gate MCP, docs, code intelligence read-only | implementation code changes |
| Backend Developer | Implement backend services | code intelligence, shell, tests, Sentinel before commit | deploy, frontend files unless needed |
| Frontend Developer | Implement frontend/mobile UI | code intelligence, shell, tests, Sentinel before commit | deploy, backend files unless needed |
| Security Reviewer | Run security scanners, write report | Sentinel MCP, code intelligence | product feature changes |
| QA Engineer | Run tests, runtime gate, smoke tests | shell, runtime, test tools | feature implementation |
| Release Manager | Finalize release, prepare deploy | docs, state, Sentinel ship_readiness | code changes |

### 3.3 State Machine
States:
- `idle`
- `in_progress`
- `pending_approval`
- `completed`
- `blocked`

Transitions:
```
idle → in_progress (phase started)
in_progress → pending_approval (agent finished artifacts)
pending_approval → completed (human approved)
pending_approval → in_progress (human rejected, agent fixes)
completed → idle (next phase start)
any → blocked (fatal error)
```

Only Team Workflow MCP can mutate state. The agent can request changes but not directly write `state.json`.

## 4. State Ledger Schema

File: `.team/state.json`

```json
{
  "schema_version": "0.1",
  "current_phase": "requirements",
  "phase_status": "in_progress",
  "active_role": "product-analyst",
  "completed_phases": [],
  "artifacts": {
    "requirements.md": {
      "status": "pending_review",
      "path": "artifacts/requirements.md",
      "commit": ""
    }
  },
  "approvals": [
    {
      "phase": "requirements",
      "decision": "approve",
      "timestamp": "2026-08-20T10:00:00Z",
      "comment": ""
    }
  ],
  "context_bundle_commit": "",
  "graph_index_version": "",
  "git_commit": "",
  "last_error": null
}
```

- `schema_version` allows future migration.
- `git_commit` records current repo commit.
- `graph_index_version` records code graph version.
- `last_error` records ERROR details for diagnosis.

## 5. Audit and Logging Design

### 5.1 Audit Log `.team/audit.jsonl`
Append-only JSON lines. Each line:

```json
{"ts":"2026-08-20T10:00:00Z","event":"approval","actor":"human","phase":"requirements","decision":"approve","commit":"abc123","details":{}}
```

Event types:
- `phase_started`
- `phase_completed`
- `approval`
- `gate_call`
- `code_intel_query`
- `agent_edit`
- `tool_call`
- `context_injection`
- `error`

Rules:
- No deletion, no editing.
- Every record includes `ts`, `event`, `actor`.
- Sensitive data must not be logged.

### 5.2 Structured Logs `.team/logs/events.jsonl`
More detailed than audit log, includes tool inputs/outputs where safe, durations, token usage.

Example:
```json
{"ts":"2026-08-20T10:01:00Z","event":"gate_call","tool":"architecture_readiness","result":"PASS","duration_ms":320,"evidence":"...","commit":"abc123"}
```

## 6. Team Workflow MCP Tool Interface

Server name: `team-workflow`

### Tools
| Tool | Description | Required Args | Returns |
|------|-------------|---------------|---------|
| `team_get_state` | Read current state | none | state JSON |
| `team_request_approval` | Ask human to approve current phase | `phase`, `comment` | approval status after human responds |
| `team_record_artifact` | Record an artifact for phase | `name`, `path`, `status` | updated state |
| `team_validate_phase` | Run validators for phase | `phase` | PASS/FAIL/ERROR with evidence |
| `team_advance` | Attempt transition to next phase | `next_phase` | new state or error |
| `team_update_context` | Regenerate context bundle | none | context commit |

### `team_validate_phase` Logic
- Requirements: artifact exists and is non-empty.
- Architecture: call System Gate `architecture_readiness`; PASS required.
- Implementation: run `sentinel check`; ensure tests pass.
- Security Review: call Sentinel `ship_readiness`; PASS required.
- QA: runtime gate configured and smoke passed.
- Release: final ship readiness + release notes.

Any unavailable tool or parse error → `ERROR`. Missing required evidence → `FAIL`. Success → `PASS`.

### Error Semantics
- `ERROR`: tool broke, connection failed, parser could not understand, missing scanner. Never sticky.
- `FAIL`: artifact incomplete, gate did not pass, consistency violation.
- `PASS`: all required checks ran and succeeded. Does not mean product is good.

No tool accepts `force`, `skip`, or `acknowledge`.

## 7. Code Intelligence Integration Design

### 7.1 Configuration `.cursor/mcp.json`
```json
{
  "mcpServers": {
    "team-workflow": {
      "type": "stdio",
      "command": "node",
      "args": ["absolute/path/to/harness_ide/dist/src/index.js"],
      "env": {
        "TEAM_ROOT": "${workspaceFolder}"
      }
    },
    "codebase-memory": {
      "type": "stdio",
      "command": "/absolute/path/to/codebase-memory-mcp"
    },
    "serena": {
      "type": "stdio",
      "command": "serena",
      "args": ["start-mcp-server", "--context", "ide", "--project", "${workspaceFolder}"]
    }
  }
}
```

### 7.2 Pre-edit Flow
1. Agent receives edit request.
2. Cursor rule triggers: call `codebase-memory.search_graph` and `trace_call_path`.
3. Team Workflow MCP logs query.
4. Agent lists exact files and symbols.
5. Human can review plan if manual checkpoint configured.

### 7.3 Post-edit Flow
1. Agent calls `codebase-memory.detect_changes`.
2. Team Workflow MCP validates affected files and logs.
3. Agent runs relevant tests/checks.
4. If high-risk, human approval required before commit.

## 8. Context Bundle Design

### 8.1 `AGENTS.md`
Generated content sections:
- Current phase and role
- Project state summary
- Recent approvals/decisions
- Architecture snapshot
- Active constraints
- notChecked items
- Next steps
- Required tools and commands

### 8.2 `PROJECT_CONTEXT.md`
Generated content sections:
- Product vision and goals
- System architecture summary
- Tech stack and repo layout
- Key decisions and ADRs
- Security posture
- Testing and deployment notes
- Risk register
- Evidence file links

### 8.3 Version Alignment
- At generation, record `git_commit` and `graph_index_version`.
- If current commit differs at next phase, context is stale.
- Agent must call `team_update_context` or re-index.

## 9. Cursor Rules and Commands Design

### 9.1 Rules `.cursor/rules/team-workflow.mdc`
```
---
description: Enforce team workflow and gate calls
alwaysApply: true
---

- Before claiming a phase complete, call team_request_approval.
- Before editing shared symbols, query codebase-memory and Serena.
- Before committing, run sentinel check.
- Do not modify .team/state.json directly.
- Do not bypass gates.
```

### 9.2 Commands
- `/team:status` — call `team_get_state` and show current phase.
- `/team:approve` — call `team_request_approval` with approve.
- `/team:reject` — call `team_request_approval` with reject and comment.
- `/team:next` — call `team_validate_phase` then `team_advance`.

## 10. Error Handling and Recovery
- MCP server crash: state file remains; restart resumes from last committed phase.
- Stale graph: version mismatch triggers re-index; high-risk edits blocked until fresh.
- Scanner unavailable: return ERROR, not FAIL; human can diagnose.
- Human rejects: phase returns to `in_progress`; agent fixes; no state loss.

## 11. Open Design Questions
- Should `team_request_approval` block the agent via a UI dialog or require human command?
- How to prevent agent from editing `.team/state.json` via shell? Possibly use file permissions.
- How to capture token usage from Cursor tool calls?
- Should logs be rotated or compacted for long projects?
- How to generate ADRs from decisions automatically?

## 12. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
