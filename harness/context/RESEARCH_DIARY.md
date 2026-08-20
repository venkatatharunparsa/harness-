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

## Next Steps
- Continue Research Sprint 3: Existing Gates Production Readiness (questions D1–D3).
- Begin deep repo analysis in `docs/REPO_ANALYSIS.md`.

---

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial diary with Sprint 1 entry |
| 0.2 | 2026-08-20 | Human lead | Added Sprint 2 entry |
