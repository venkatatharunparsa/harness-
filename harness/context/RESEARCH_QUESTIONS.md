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
- `answered` — resolved with evidence, recorded in `docs/RESEARCH.md` or `docs/REPO_ANALYSIS.md`.
- `blocked` — cannot answer yet due to missing access or tooling.

---

## A. Cursor Enforcement & Hook Capabilities

### A1. What exactly can Cursor’s `beforeShellExecution` hook block?
- **Priority:** Critical
- **Status:** open
- **Question:** Can it inspect the command string, working directory, and environment? Can it block all shell commands that attempt to write to `.team/` or `.system-gate/`?
- **Evidence needed:** Cursor official docs, community reports, direct experiment in Cursor.
- **Why it matters:** Determines if we can use shell guard to protect critical files.

### A2. Can Cursor block file edits outside shell, e.g., direct file write tools?
- **Priority:** Critical
- **Status:** open
- **Question:** Does Cursor expose any hook that intercepts its own file editing tools, not just shell?
- **Evidence needed:** Cursor docs on hooks, experimentation.
- **Why it matters:** If not, we must protect state files via filesystem permissions or external watcher.

### A3. How does Cursor’s `beforeSubmitPrompt` work in practice?
- **Priority:** High
- **Status:** open
- **Question:** Can it pause the agent until a human responds? Can it block phase completion or force a review?
- **Evidence needed:** Cursor docs, field tests.
- **Why it matters:** Could serve as the approval gate inside Cursor.

### A4. Can Cursor rules reliably force tool calls?
- **Priority:** High
- **Status:** open
- **Question:** If we write a rule saying “before editing shared symbols, query codebase-memory,” does the agent obey consistently? What is the measured compliance rate?
- **Evidence needed:** Controlled tests in Cursor with logging.
- **Why it matters:** Determines if rules are guidance or enforcement.

### A5. Can we run a background watcher process alongside Cursor to monitor file changes and alert/block?
- **Priority:** High
- **Status:** open
- **Question:** Is there a supported way to integrate an external watcher on Windows/macOS/Linux? Can it detect edits to `.team/state.json` and notify Cursor or human?
- **Evidence needed:** Filesystem watcher libraries, Cursor integration possibilities.
- **Why it matters:** A sidecar watcher could give us stronger enforcement than hooks.

---

## B. State Protection & Approval UX

### B1. Where should `.team/state.json` live so the agent cannot directly write it?
- **Priority:** Critical
- **Status:** open
- **Question:** Options: outside workspace, read-only filesystem permissions, special MCP-only storage. Which is most practical and secure?
- **Evidence needed:** Cursor file tool behavior, OS permissions, MCP server capabilities.
- **Why it matters:** State integrity is foundational.

### B2. What is the best human approval mechanism inside Cursor?
- **Priority:** Critical
- **Status:** open
- **Question:** Commands? Blocking MCP tool? External UI? Cursor-specific popup? Which is least friction and most reliable?
- **Evidence needed:** Cursor MCP capabilities, UX experiments.
- **Why it matters:** If approval is too painful, the human will bypass it.

### B3. Can an MCP tool block the agent until human provides input?
- **Priority:** High
- **Status:** open
- **Question:** Does Cursor support long-running MCP tool calls that wait for user input? Or does it timeout?
- **Evidence needed:** MCP spec, Cursor implementation.
- **Why it matters:** Determines if approval can be a blocking checkpoint.

### B4. How to prevent agent from using shell to overwrite state files?
- **Priority:** Critical
- **Status:** open
- **Question:** Can we disable shell for certain roles or enforce command allowlists? Or use filesystem ACLs?
- **Evidence needed:** Cursor tool permissions, role-based tool gating.
- **Why it matters:** Shell is the easiest bypass route.

---

## C. Code Intelligence & Hybrid Design

### C1. Can `codebase-memory-mcp` be extended/wrapped to add GitNexus-style process tracing and confidence scores?
- **Priority:** High
- **Status:** open
- **Question:** Does it expose raw graph data programmatically? Can we post-process its MCP outputs to group call paths into flows and add confidence?
- **Evidence needed:** Source code, API docs, experiments.
- **Why it matters:** We want best of GitNexus without license risk.

### C2. What are the exact Dart/Flutter limitations of `codebase-memory-mcp`?
- **Priority:** High
- **Status:** open
- **Question:** Which Flutter-specific relationships does it miss? Can Serena fully compensate? What additional Dart Analyzer steps are needed?
- **Evidence needed:** Controlled tests on a Flutter sample repo.
- **Why it matters:** Determines if our Flutter workflow is safe.

### C3. How to handle monorepo multi-root indexing with `codebase-memory-mcp`?
- **Priority:** High
- **Status:** open
- **Question:** Does one MCP process index multiple package roots? How to configure exclusions for generated/build dirs? Can we query across backend/web/mobile in one graph?
- **Evidence needed:** Tool docs, monorepo test.
- **Why it matters:** Our target project is likely a monorepo.

### C4. What is the token cost of each graph query, and how do we cap outputs?
- **Priority:** Medium
- **Status:** open
- **Question:** What is the typical response size for `trace_call_path`, `get_architecture`, `detect_changes`? How to summarize before injecting into context?
- **Evidence needed:** Measurements on sample repo.
- **Why it matters:** Prevents token waste and keeps agent focused.

### C5. Do we need a separate “process tracing” layer, or can `codebase-memory-mcp` + our own aggregation suffice?
- **Priority:** Medium
- **Status:** open
- **Question:** GitNexus’s process clustering is valuable. Can we reconstruct similar insights from call paths and HTTP edges?
- **Evidence needed:** Compare outputs on same repo.
- **Why it matters:** Avoid building unnecessary complexity.

---

## D. Existing Gates Production Readiness

### D1. Is System Gate production-ready for our needs?
- **Priority:** Critical
- **Status:** open
- **Question:** Does it validate all required architecture aspects we care about? Can it be extended with new checks? Is it stable and tested?
- **Evidence needed:** System Gate source/architecture, field reports.
- **Why it matters:** We may need to improve or replace it.

### D2. Is Sentinel production-ready for a commercial SaaS?
- **Priority:** Critical
- **Status:** open
- **Question:** Does it cover our target stacks (FastAPI/Node/Flutter)? Are scanner integrations reliable? Can it produce ship_readiness without bypass?
- **Evidence needed:** Sentinel benchmarks, source review.
- **Why it matters:** Security gate is a core pillar.

### D3. Do we need additional gates beyond System Gate and Sentinel (e.g., reliability, performance, observability)?
- **Priority:** High
- **Status:** open
- **Question:** What production-readiness dimensions are not covered by current gates? How do we add them without inventing PASS?
- **Evidence needed:** Gap analysis, reference architectures.
- **Why it matters:** We want production-ready, not just secure.

---

## E. Open-Source Repo Deep Analysis

We will create `docs/REPO_ANALYSIS.md` and populate it with detailed findings for each candidate repo. Each analysis must answer:
- What specific workflows/patterns can we borrow?
- What would it take to implement those ourselves?
- Are there any reusable components with compatible licenses?
- What are the risks and validation needs?

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
- Record all findings in `docs/REPO_ANALYSIS.md` and `context/RESEARCH_DIARY.md`.
- Update this document with status changes.

## Priority Order for Research
1. Cursor enforcement/hooks (A1–A5, B1–B4).
2. Code intelligence hybrid (C1–C5).
3. Existing gates production readiness (D1–D3).
4. Repo deep analysis (E) — ongoing as needed.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
