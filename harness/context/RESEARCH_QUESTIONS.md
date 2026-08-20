# Research Questions: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Living document  
**Owner:** Human lead  
**Date:** 2026-08-20

## Purpose
This document tracks every unresolved question that must be answered with evidence before we commit to implementation. Each question is assigned a category, priority, status, and evidence source. Nothing is assumed. We research until we have confidence data, not guesses.

## Status Values
- `open` — not yet researched.
- `in-progress` — research started.
- `answered` — resolved with evidence, recorded in research docs.
- `blocked` — cannot answer yet due to missing access or tooling.

---

## A. Cursor Enforcement & Hook Capabilities

### A1. What exactly can Cursor’s `beforeShellExecution` hook block?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### A2. Can Cursor block file edits outside shell, e.g., direct file write tools?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### A3. How does Cursor’s `beforeSubmitPrompt` work in practice?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### A4. Can Cursor rules reliably force tool calls?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### A5. Can we run a background watcher process alongside Cursor to monitor file changes and alert/block?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

---

## B. State Protection & Approval UX

### B1. Where should `.team/state.json` live so the agent cannot directly write it?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### B2. What is the best human approval mechanism inside Cursor?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### B3. Can an MCP tool block the agent until human provides input?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

### B4. How to prevent agent from using shell to overwrite state files?
- **Status:** answered
- **Evidence:** `docs/CURSOR_ENFORCEMENT_RESEARCH.md`

---

## C. Code Intelligence & Hybrid Design

### C1. Can `codebase-memory-mcp` be extended/wrapped to add GitNexus-style process tracing and confidence scores?
- **Status:** answered
- **Evidence:** `docs/CODE_INTELLIGENCE_RESEARCH.md`

### C2. What are the exact Dart/Flutter limitations of `codebase-memory-mcp`?
- **Status:** answered
- **Evidence:** `docs/CODE_INTELLIGENCE_RESEARCH.md`

### C3. How to handle monorepo multi-root indexing with `codebase-memory-mcp`?
- **Status:** answered
- **Evidence:** `docs/CODE_INTELLIGENCE_RESEARCH.md`

### C4. What is the token cost of each graph query, and how do we cap outputs?
- **Status:** answered
- **Evidence:** `docs/CODE_INTELLIGENCE_RESEARCH.md`

### C5. Do we need a separate “process tracing” layer, or can `codebase-memory-mcp` + our own aggregation suffice?
- **Status:** answered
- **Evidence:** `docs/CODE_INTELLIGENCE_RESEARCH.md`

---

## D. Existing Gates Production Readiness

### D1. Is System Gate production-ready for our needs?
- **Status:** answered
- **Evidence:** `docs/GATES_RESEARCH.md`

### D2. Is Sentinel production-ready for a commercial SaaS?
- **Status:** answered
- **Evidence:** `docs/GATES_RESEARCH.md`

### D3. Do we need additional gates beyond System Gate and Sentinel (e.g., reliability, performance, observability)?
- **Status:** answered
- **Evidence:** `docs/GATES_RESEARCH.md`

---

## E. Open-Source Repo Deep Analysis

Initial target repos:
1. GitNexus
2. codebase-memory-mcp
3. Serena
4. CodeGraph
5. DeepSeek Harness
6. DeerFlow
7. OpenHands / Goose
8. Temporal (durable workflow patterns)
9. OpenShell / Pipelock (policy enforcement)
10. TencentDB Agent Memory (layered memory)
11. Agent Skills spec / AGENTS.md / Open Knowledge Format
12. Constellation existing System Gate & Sentinel

---

## Research Methods
- Use Perplexity Comet for targeted questions.
- Read source repos directly where possible.
- Run controlled experiments in Cursor for behavior validation.
- Record all findings in research docs and `context/RESEARCH_DIARY.md`.
- Update this document with status changes.

## Priority Order for Research
1. Cursor enforcement/hooks — COMPLETED.
2. Code intelligence hybrid — COMPLETED.
3. Existing gates production readiness — COMPLETED.
4. Repo deep analysis — ongoing as needed.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
| 0.2 | 2026-08-20 | Human lead | Marked A and B answered after Sprint 1 |
| 0.3 | 2026-08-20 | Human lead | Marked C answered after Sprint 2 |
| 0.4 | 2026-08-20 | Human lead | Marked D answered after Sprint 3 |
