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

### Next Steps
- Continue Research Sprint 2: Code Intelligence & Hybrid Design (questions C1–C5).
- Deep repo analysis in `docs/REPO_ANALYSIS.md`.

---

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial diary with Sprint 1 entry |
