# Research Diary: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Living document  
**Owner:** Human lead  
**Date:** 2026-08-20

## Purpose
This diary records research activities, key findings, decisions influenced, and field observations. It is a chronological log of evidence gathering and analysis.

---

## 2026-08-20 — Research Sprint 1: Cursor Enforcement & Hook Capabilities

### Summary
We investigated Cursor's enforcement capabilities in depth using primary sources and community evidence. Key findings:

- Cursor rules are guidance only; enforcement requires hooks and external state.
- `preToolUse` hook can block direct file edits to protected paths.
- `beforeShellExecution` hook can block shell commands targeting protected paths.
- `beforeSubmitPrompt` can block prompts but cannot pause for approval.
- MCP elicitation has a hardcoded 60-second timeout, making it unsuitable as sole approval mechanism.
- State files should be stored outside workspace with `.cursorignore` and MCP-only writes.
- Background watchers can detect but not block; use hooks for prevention.

### Decisions Influenced
- Enforcement architecture: hooks + external state + watcher, not rules.
- Approval UX: state machine with Cursor commands `/team:approve`, `/team:reject`.
- State protection: outside workspace, `.cursorignore`, read-only where possible, MCP writes.

### Evidence Recorded
- Full consolidated findings: `docs/CURSOR_ENFORCEMENT_RESEARCH.md`
- Research questions status updated: `context/RESEARCH_QUESTIONS.md`

---

## 2026-08-20 — Research Sprint 2: Code Intelligence & Hybrid Design

### Summary
We investigated `codebase-memory-mcp`, `Serena`, `GitNexus`, and token/output management. Key findings:

- `codebase-memory-mcp` can be wrapped, not forked, to add GitNexus-style confidence and impact analysis.
- Dart/Flutter limitations are significant; Serena/Dart LSP + Flutter toolchain required for semantic safety.
- Monorepo root indexing works, but symlinks are skipped; validate pnpm and use `.cbmignore`.
- Token outputs are small (200–3,000), but no built-in truncation; wrapper must cap and summarize.
- Full process tracing is not required for v1; call-path + HTTP edge aggregation is enough.

### Decisions Influenced
- Hybrid code intelligence architecture confirmed.
- Wrapper MCP will enrich `codebase-memory-mcp` outputs.
- Flutter requires additional LSP/toolchain beyond graph engine.
- Monorepo exclusions and token capping added to Phase 0 validation.

### Evidence Recorded
- Full consolidated findings: `docs/CODE_INTELLIGENCE_RESEARCH.md`
- Research questions status updated: `context/RESEARCH_QUESTIONS.md`

---

## 2026-08-20 — Research Sprint 3: Existing Gates Production Readiness

### Summary
We investigated System Gate, Sentinel, and additional production-readiness gates. Key findings:

- System Gate is necessary but insufficient; needs fitness functions.
- Sentinel covers core static scanning but misses supply chain, DAST, runtime, API authz, mobile, IaC, and secrets management.
- Six additional gates needed: reliability, performance, observability, deployment, scalability, cost.
- All gates must be deterministic, evidence-based, and exit-code driven.

### Decisions Influenced
- System Gate v2: schema + fitness functions.
- Sentinel v2: stage-based security gates.
- New production-gates layer for QA/Release phases.
- All gates remain deterministic.

### Evidence Recorded
- Full consolidated findings: `docs/GATES_RESEARCH.md`
- Research questions status updated: `context/RESEARCH_QUESTIONS.md`

---

## 2026-08-20 — Source Inspection: codebase-memory-mcp

### Summary
We cloned and inspected `codebase-memory-mcp` source in Cursor. Major source-verified corrections:

- README node/edge catalog is incomplete; use `get_graph_schema`.
- `detect_changes` has no confidence/risk; only `trace_path` supports risk labels.
- No public library API; use as MCP binary + CLI.
- Renames often trigger FULL rebuilds.
- Watcher is git-poll only.
- Flutter/Dart grammar-level only.
- pnpm symlinks skipped.
- Hybrid LSP is embedded, no external language servers.

### Decisions Influenced
- Wrapper MCP is mandatory for enrichment.
- Flutter workflow requires Serena + Dart Analyzer.
- Need explicit Windows integration handling.
- `trace_path` with format:"json" is the correct tool name.

### Evidence Recorded
- Source notes: `context/CODEBASE_MEMORY_MCP_SOURCE_NOTES.md`
- Updated repo analysis: `docs/REPO_ANALYSIS.md`

---

## Next Steps
- Continue deep source inspection: Serena.
- Then CodeGraph and other repo analyses.
- Proceed to Phase 0 tool validation.

---

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial diary with Sprint 1 entry |
| 0.2 | 2026-08-20 | Human lead | Added Sprint 2 entry |
| 0.3 | 2026-08-20 | Human lead | Added Sprint 3 entry |
| 0.4 | 2026-08-20 | Human lead | Added source inspection entry |
