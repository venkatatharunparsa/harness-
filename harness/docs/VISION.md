# Vision: Constellation Team Harness (CTH)

## Purpose
Build a harness that turns Cursor, Codex, Claude Code, or Antigravity into a disciplined engineering team capable of producing production-ready SaaS applications, mobile applications, websites, and autonomous workflows — not vibe-coded prototypes.

This is not a toy. This is not a collection of scattered MCPs. This is a system that gives agents repo-wide memory, impact awareness, structured team roles, enforced gates, and human-controlled transitions.

## Problem Statement
Coding agents today fail in predictable ways:
- They lose context across sessions and ignore retrieval-based memory.
- They write unnecessary code and introduce security issues.
- They change one part of the codebase and break others without understanding impact.
- They skip architecture, security checks, and testing to produce output faster.
- They rarely produce software that is truly production-ready in terms of scalability, reliability, security, observability, and maintainability.

We need a harness that pushes agents to their limits and forces them to work with the discipline of an engineering team.

## Core Principles
1. **Verdicts come from evidence, never from prose.**  
   PASS must be based on files on disk, exit codes, schema validation, and scanner results — never on LLM judgment.

2. **The orchestration lives outside the model.**  
   Critical transitions are enforced by external state, validators, and hooks. The agent is a worker; the harness is the manager.

3. **Preloaded context beats retrieval that can be ignored.**  
   Agents consume rules and project context that are always present, not MCP tools they forget to call.

4. **Everything is measured, and unused parts are deleted.**  
   We keep what works in the field and remove what does not, based on evidence, not taste.

5. **The human is the final decision maker.**  
   The agent recommends, the human approves. Overrides require config changes, not prompt persuasion.

6. **Honesty about limitations is a feature.**  
   We explicitly track notChecked areas, partial coverage, and what PASS does not mean.

## What We Are Building
A modular harness inside the IDE that provides:

1. **Team Workflow Engine**
   - Role definitions: Product Analyst, Architect, Backend Developer, Frontend Developer, Security Reviewer, QA Engineer, Release Manager.
   - Phase transitions: Requirements → Architecture → Implementation → Security → QA → Release.
   - Human approval gates between phases.
   - State ledger (`.team/state.json`) + audit log (`.team/audit.jsonl`) as the single source of truth.

2. **Code Intelligence Layer**
   - Persistent repo-wide structural graph with caller/callee, routes, imports, tests.
   - Impact analysis / blast radius before changes.
   - Git-diff change detection.
   - LSP-grade symbol editing where needed.
   - Primary tool candidates: `codebase-memory-mcp` + `Serena`.

3. **Context & Memory System**
   - Git-backed context bundle: `AGENTS.md` and `PROJECT_CONTEXT.md` generated after each phase.
   - Layered context: summary → decisions → detailed artifacts.
   - Optional project memory for decisions/ADRs, not chat logs.
   - No separate retrieval MCP unless field evidence proves it is used.

4. **Exact Codebase Context & Codebase Management with Versions**
   - The harness must provide **exact code context at symbol level**, not just a fuzzy semantic search.
   - When an agent touches a function, class, route, or type, it must know:
     - Its precise declaration and line range.
     - All semantic references and implementations.
     - Its callers and callees.
     - Related tests and generated files.
   - Codebase context must be **version-aligned**: the graph index, context bundle, and state ledger must record the Git commit/hash they were generated from.
   - We will maintain **versioned snapshots** of the structural code graph and context artifacts so we can:
     - Reconstruct what the agent knew at any point in time.
     - Compare code relationships across Git revisions.
     - Detect stale context and force re-index before high-risk edits.
   - This is not “search plus chat history.” This is a managed, versioned representation of the repository that evolves with the code.

5. **Enforcement Layer**
   - Integrate with existing Constellation gates: System Gate (architecture) and Sentinel (security).
   - Cursor rules and commands to force tool usage and phase validation.
   - Soft hooks where Cursor allows; external validators do the hard work.

6. **Logging, Observability, and Audit Trail**
   - The harness must produce **structured, append-only logs** for every significant event:
     - Phase transitions and human approvals.
     - Gate calls and their results (PASS/FAIL/ERROR).
     - Code intelligence queries and responses.
     - Agent edits with before/after file hashes and Git diffs.
     - Tool calls, commands run, and scan outputs.
     - Context injections and version snapshots.
   - Logs must be queryable and human-readable: `.team/logs/` directory with JSONL and optional summaries.
   - The audit trail must make it possible to answer:
     - “Why did the agent change this file?”
     - “Which evidence did it see before editing?”
     - “Which gate approved this phase?”
     - “What tests/scanners were run and when?”
   - This supports debugging, accountability, and possible research evaluation.

7. **Measurement & Research Backing**
   - Log every phase transition, gate call, tool call, human decision, and issue.
   - Maintain a benchmark ledger and field reports.
   - Preserve evidence for possible future publication.

## What We Are Not Building
- Another agent framework (LangGraph, CrewAI, etc.).
- A standalone autonomous agent runtime (DeerFlow, Hermes).
- A company-wide enterprise search or knowledge platform (Onyx, RAGFlow).
- A memory-only MCP that agents ignore.
- A complex multi-agent parallel orchestrator with git worktrees in v1.
- Any system that requires the human to trust the model’s self-assessment.

## Success Criteria
- A single developer can install the harness inside Cursor and build a real production-quality SaaS/mobile app from a large PRD.
- The agent consistently queries code intelligence before edits, resulting in fewer broken changes.
- All phases require human approval, and the human can see exactly what was produced and why.
- Security and architecture gates are enforced automatically, not forgotten.
- The system is measurable: we can show gate call rates, context effectiveness, and impact accuracy.
- The harness is portable to Claude Code, Codex, and Antigravity with minimal changes.
- Logs and versioned context allow us to reconstruct every decision and evidence trail.

## Primary Host and Stack for First Validation
- Host: Cursor (IDE-native, not CLI).
- Backend: Node.js or FastAPI.
- Frontend/Mobile: React Native or Flutter.
- First dogfood project: a prototype built from a large PRD.

## Non-Negotiable Constraints
- No proprietary/noncommercial licenses that block commercial use (e.g., PolyForm Noncommercial).
- All critical state must be external to the model and version-controlled.
- The human must approve every phase transition.
- No bypass flags (`force`, `skip`, `acknowledge`) in gates.
- We do not invent PASS. If a check did not run, the answer is ERROR.
- Codebase context must be version-aligned and auditable.

## Open Questions to Resolve Later
- Exact naming of the harness and repository.
- Whether to adopt Serena now or later.
- How to handle monorepo code intelligence accurately.
- Specific role acceptance criteria and artifact schemas.
- Deep Cursor hook limitations and workarounds.
- How much historical graph versioning to retain without bloating the repo.

## Document Governance
This document is the anchor. All requirements, architecture, and decisions must trace back to it. Changes require explicit human approval and must be recorded in the decision log.
