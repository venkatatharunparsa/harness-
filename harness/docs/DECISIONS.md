# Decision Log: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## Purpose
This document records all significant decisions made during the design and development of CTH. Each decision includes context, options considered, rationale, consequences, and approval status. It serves as the audit trail for why the system is built the way it is.

## Format
Each decision uses the following structure:

```
### D-XXX: Title
- **Status:** Proposed / Accepted / Rejected / Superseded
- **Date:** YYYY-MM-DD
- **Context:** ...
- **Options Considered:** ...
- **Decision:** ...
- **Rationale:** ...
- **Consequences:** ...
- **Approved by:** Human lead
```

---

## D-001: Primary Host is Cursor
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** The harness must run inside an IDE, not as a CLI orchestrator. The human prefers Cursor as the primary environment.
- **Options Considered:** Cursor, Claude Code, Codex, Antigravity.
- **Decision:** Build for Cursor first, with host abstraction to support Claude Code, Codex, and Antigravity later.
- **Rationale:** Cursor supports MCP, rules, commands, and is the most familiar environment. Its soft enforcement can be compensated with external validators and human approval.
- **Consequences:** Some enforcement will be soft; we must rely on external gates and human control. Host-specific adapters must be isolated.

---

## D-002: Human-in-the-Loop Approval Model
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** The agent must not self-advance through phases. The human is the final decision maker.
- **Options Considered:** Fully autonomous, human-driven role switching, hybrid.
- **Decision:** Hybrid model — agent recommends transitions, human explicitly approves each phase.
- **Rationale:** Keeps human control while reducing manual overhead. Prevents agent from bypassing gates.
- **Consequences:** Requires approval UX commands and a state machine that blocks self-advancement.

---

## D-003: Code Intelligence Stack — `codebase-memory-mcp` + `Serena`
- **Status:** Accepted (with staged adoption)
- **Date:** 2026-08-20
- **Context:** Need persistent repo graph, impact analysis, and precise symbol editing for Node/FastAPI/Flutter stack.
- **Options Considered:** `codebase-memory-mcp`, `GitNexus`, `CodeGraph`, `Serena`.
- **Decision:** Adopt `codebase-memory-mcp` as primary graph engine. Add `Serena` later for precise semantic editing when needed. Exclude `GitNexus` due to PolyForm Noncommercial license. Evaluate `CodeGraph` as pilot alternative if needed.
- **Rationale:** `codebase-memory-mcp` is MIT, local, persistent, has `detect_changes` and `trace_call_path`, and works well for TypeScript/Python. `Serena` adds LSP-grade refactoring. `GitNexus` license is commercially unsafe.
- **Consequences:** We must validate `codebase-memory-mcp` on Flutter/Dart; static graph may miss dynamic relationships. Serena setup adds complexity, so we defer it.

---

## D-004: Context & Memory Strategy — Git-backed Markdown, no retrieval MCP in v1
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Agents ignore retrieval-based memory MCPs. Constellation field evidence showed Skills MCP zero usage.
- **Options Considered:** Git-backed context files, TencentDB Agent Memory, Mem0, custom retrieval MCP.
- **Decision:** Start with Git-backed `AGENTS.md` and `PROJECT_CONTEXT.md` generated after each phase. No separate memory MCP until evidence shows need.
- **Rationale:** Preloaded context is consumed; retrieval is ignored. Version-controlled files provide auditability and simplicity.
- **Consequences:** Context must be regenerated after each phase; no automatic cross-session recall beyond these files.

---

## D-005: Enforcement Approach — External Validators + Cursor Rules/Commands
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Cursor’s native hooks are soft (stop is soft, loop_limit expires). Need hard enforcement for critical gates.
- **Options Considered:** Rely on Cursor hooks, build external CLI daemon, use Git hooks, use external validators.
- **Decision:** Use external validators (System Gate, Sentinel) as hard gates; use Cursor rules and commands to guide agent; accept soft limitations and rely on human approval.
- **Rationale:** External validators produce evidence-based PASS/FAIL/ERROR. Cursor cannot fully hard-stop, so human is backstop.
- **Consequences:** Some agent actions may slip through; we must measure and adjust.

---

## D-006: Custom Team Workflow MCP as Orchestration Layer
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Need a single interface for roles, approvals, state, and gate calls, not a pile of independent MCPs.
- **Options Considered:** Use existing MCPs directly, build a wrapper MCP, build separate orchestrator service.
- **Decision:** Build a `team-workflow` MCP that wraps other tools and owns state transitions.
- **Rationale:** Provides one interface, enforces workflow, and logs events. It keeps orchestration outside the model.
- **Consequences:** More initial work; but easier to extend and maintain.

---

## D-007: Documentation-Driven Development
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Need clear vision, requirements, and design before implementation to avoid drift.
- **Options Considered:** Build first, document later; document as we go; full upfront documentation.
- **Decision:** Full upfront foundation docs (VISION, PROPOSAL, REQUIREMENTS, ARCHITECTURE, DESIGN, DECISIONS, ROADMAP, VALIDATION) reviewed and approved before coding.
- **Rationale:** Ensures alignment and traceability. Reduces rework. Provides a research base.
- **Consequences:** Slower start, but higher quality output.

---

## D-008: Research and Publication Evidence Capture from Day One
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Potential future research paper. Need logs, metrics, and field reports.
- **Options Considered:** Add logging later, build logging now.
- **Decision:** Build structured logging and audit trail from day one, and maintain a research diary.
- **Rationale:** Data must be captured as events happen, not reconstructed. Enables publication and rigorous evaluation.
- **Consequences:** Extra overhead, but justified by potential paper and system accountability.

---

## D-009: Role Definitions and Phase Sequence
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Need an engineering team workflow inside Cursor.
- **Options Considered:** Simplified roles, more granular roles, different phase order.
- **Decision:** Seven roles (Product Analyst, Architect, Backend Developer, Frontend Developer, Security Reviewer, QA Engineer, Release Manager) and six phases (Requirements, Architecture, Implementation, Security Review, QA, Release).
- **Rationale:** Covers end-to-end production workflow without over-fragmentation. Matches existing greenfield sequence.
- **Consequences:** Implementation must support these roles and phases.

---

## D-010: Repository Structure and Tooling
- **Status:** Accepted
- **Date:** 2026-08-20
- **Context:** Need a clear monorepo layout for docs, context, and harness code.
- **Options Considered:** Separate repos for docs and code, single repo with folders.
- **Decision:** Single repo with `docs/`, `context/`, `harness_ide/` directories. Use Git from first commit. Use Antigravity for file creation and editing until harness itself is ready.
- **Rationale:** Keeps everything versioned together, easy to navigate.
- **Consequences:** Harness code and docs share history; good for traceability.

---

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial decision log draft |
