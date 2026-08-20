# Validation Plan: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Purpose
This document defines how we validate that CTH meets its requirements and actually works in practice. It covers tool validation, component testing, integration testing, end-to-end dogfood evaluation, metrics collection, and acceptance criteria. Every claim about the harness must be verified through evidence, not assumption.

## 2. Scope
Validation covers:
- Selected external tools: `codebase-memory-mcp`, optional `Serena`, System Gate, Sentinel.
- Custom components: `team-workflow` MCP, state ledger, audit logs, context generator.
- Cursor integration: `.cursor/mcp.json`, rules, commands, hooks.
- End-to-end workflow: Requirements → Architecture → Implementation → Security Review → QA → Release.
- Production readiness of the harness itself, not only the applications it builds.

## 3. Validation Strategy
We use a layered approach:

1. **Unit validation** — test each function/module in isolation.
2. **Integration validation** — test MCP tools, state transitions, and external gate calls together.
3. **Tool validation** — verify each external tool against a known repository before relying on it.
4. **Workflow validation** — test full phase transitions with human approvals.
5. **Dogfood validation** — build a real prototype from a large PRD and measure outcomes.

All validation results are recorded in `context/TOOL_EVALUATION.md`, `context/RESEARCH_DIARY.md`, and `.team/logs/`.

## 4. Test Environment
- **OS:** Windows (primary), with future macOS/Linux portability checks.
- **IDE:** Cursor, with project-level MCP config.
- **Languages:** TypeScript/JavaScript, Python/FastAPI, Dart/Flutter.
- **Repos:** small test repo, medium demo repo, and later the actual PRD prototype repo.
- **Isolation:** Local-only execution; no source code leaves the machine for graph indexing.

## 5. Tool Validation Checklists

### 5.1 `codebase-memory-mcp`
- **Connection:** MCP server starts inside Cursor; `index_status` returns correct repo path.
- **Indexing:** Initial index completes; file count and symbol count match expectations.
- **Language support:**
  - TypeScript/JavaScript: symbols, imports, JSX components, call paths.
  - Python/FastAPI: routes, functions, imports.
  - Dart/Flutter: structural symbols; note limitations.
- **Impact analysis:**
  - `trace_call_path` inbound/outbound returns correct callers/callees.
  - `detect_changes` on a controlled edit returns affected files and symbols.
- **Incremental freshness:**
  - Edit one file → re-index only changed file.
  - Delete/rename file → graph updates.
  - Branch switch → index detects change.
- **Performance:**
  - Measure indexing time on a medium repo.
  - Measure query latency for `trace_call_path` and `detect_changes`.
- **License:** MIT confirmed.

### 5.2 Serena (optional / deferred)
- **Connection:** MCP server starts with Cursor.
- **Symbol lookup:** `find_symbol` returns exact declaration.
- **References:** `find_referencing_symbols` accurate for TypeScript/Python/Dart.
- **Rename:** `rename_symbol` updates all references correctly.
- **Diagnostics:** `get_diagnostics_for_file` reports language-server errors.
- **Decision gate:** Only adopt if a real refactoring need appears and benefits are measured.

### 5.3 System Gate
- **Architecture readiness:** `architecture_readiness` returns PASS only when `.system-gate/architecture.json` exists and is valid.
- **Fail closed:** missing required section → FAIL.
- **No bypass:** `force`/`skip` flags rejected.

### 5.4 Sentinel
- **Scanner execution:** `sentinel check` runs gitleaks, opengrep, trivy.
- **Verdict semantics:** ERROR if scanner didn’t run; FAIL on real findings; PASS only when clean.
- **No bypass:** `ship_readiness` accepts no force/skip/acknowledge.
- **Ledger:** `.sentinel/run-ledger.json` binds to tree hash.

### 5.5 Cursor MCP/Rules
- **MCP config:** project-level `.cursor/mcp.json` loads; absolute paths resolve.
- **Rules:** `.cursor/rules/*.mdc` load and are always applied.
- **Commands:** `/team:status`, `/team:approve`, `/team:reject`, `/team:next` call team-workflow MCP.

## 6. Component Validation

### 6.1 Team Workflow MCP
| Test | Expected |
|------|----------|
| `team_get_state` | Returns valid state JSON |
| `team_record_artifact` | Adds artifact entry; rejects invalid path |
| `team_request_approval` | Creates pending approval; human decision recorded |
| `team_validate_phase` | Calls appropriate gate; returns PASS/FAIL/ERROR |
| `team_advance` | Only advances when previous phase completed/approved |
| `team_update_context` | Generates context bundle; records commit hash |

### 6.2 State Ledger
- Valid JSON schema at all times.
- Atomic writes; no partial/corrupt state.
- Versioned (`schema_version`).
- Agent cannot directly write except through MCP.

### 6.3 Audit Log
- Append-only JSONL.
- Each event includes required fields.
- No sensitive data in logs.
- Logs queryable with basic scripts.

### 6.4 Context Bundle
- `AGENTS.md` contains current phase/role/decisions.
- `PROJECT_CONTEXT.md` contains architecture summary and risk register.
- Version aligned with Git commit and graph index version.
- Stale detection works when commit mismatch.

## 7. Integration Validation

### 7.1 Phase Transition Flow
- Start in Requirements.
- Agent produces requirements artifact.
- Agent requests approval.
- Human approves.
- State moves to completed, then next phase starts.
- If human rejects, phase returns to in_progress.

### 7.2 Code Intelligence Integration
- Pre-edit: agent queries graph before changing shared symbol.
- Post-edit: agent runs `detect_changes` and logs affected files.
- Logs show both query and result.

### 7.3 Gate Enforcement
- Architecture phase: System Gate called; transition blocked on FAIL.
- Security Review phase: Sentinel called; transition blocked on non-PASS.
- Implementation: Sentinel check before commits.

## 8. End-to-End Dogfood Validation
Use the harness to build a prototype from a large PRD with:

- Backend: Node.js or FastAPI.
- Frontend/Mobile: React Native or Flutter.
- Multiple APIs and production-like requirements.

### 8.1 Success Criteria
- All phases executed in order.
- Human approvals recorded.
- Architecture and security gates passed.
- Context bundle updated after each phase.
- Code intelligence used before/after significant edits.
- Prototype builds, tests pass, and runtime smoke succeeds.
- Production-breaking issues caught by the harness are documented.

### 8.2 Failure Criteria
- Agent skips gates without detection.
- State ledger corrupted.
- Context bundle stale/incorrect.
- Code graph returns consistently wrong impact.
- Human approval bypassed.
- Harness becomes too burdensome and is abandoned.

## 9. Metrics and Measurement

### 9.1 Process Metrics
- Gate call rate without reminder (%).
- Phase approval/rejection ratio.
- Time per phase.
- Number of loops back to fix issues.

### 9.2 Tool Metrics
- Indexing time.
- Query latency.
- `detect_changes` precision/recall on known changes.
- MCP startup time.
- Memory/CPU usage.

### 9.3 Outcome Metrics
- Number of production-breaking issues caught before release.
- Number of issues found after release.
- Code review time spent by human.
- Agent edit correctness rate.
- Context bundle staleness rate.

### 9.4 Evaluation Protocol
- Record baseline behavior without harness on a small task.
- Record behavior with harness on same/similar task.
- Compare metrics and document differences.
- Use structured logs and field reports as evidence.

## 10. Bug and Issue Tracking
- Log all bugs in `context/RESEARCH_DIARY.md` or GitHub Issues.
- Each issue must include:
  - Expected vs actual behavior.
  - Reproduction steps.
  - Tool and version.
  - Log excerpts.
  - Severity and impact.
- Fixes must be validated with regression tests.

## 11. Exit Criteria per Phase
Refer to `ROADMAP.md` for phase-specific exit criteria. Validation ends when:
- Phase 5 dogfood exits are met.
- Critical issues are resolved or explicitly accepted as limitations.
- `VALIDATION_PLAN.md` metrics are collected.
- Decision log is updated with evidence-based keep/cut choices.

## 12. Risks and Contingencies
| Risk | Contingency |
|------|-------------|
| `codebase-memory-mcp` fails in Cursor | Pivot to `CodeGraph` or fallback to Cursor native + manual impact checks |
| Flutter/Dart graph unreliable | Add Serena/Dart Analyzer; rely on tests |
| Cursor enforcement too soft | Increase human approval gates; use pre-commit hooks |
| MCP overhead too high | Disable non-essential MCPs; profile |
| State/log corruption | Atomic writes, backups, schema migration |
| Tool license change | Swap module (modular architecture) |

## 13. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
