# Product Requirements Document (PRD): Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Introduction
The Constellation Team Harness (CTH) is a discipline-enforcing layer that runs inside Cursor (and later Claude Code, Codex, Antigravity) to guide coding agents through a structured engineering workflow. It combines team roles, evidence-based gates, versioned codebase context, code intelligence, and human-approval checkpoints to produce production-ready software.

This document defines the product requirements for the harness itself, not the target applications it will build.

## 2. Problem Statement
Current IDE coding agents lack the memory, impact awareness, and discipline needed for production software. They lose context, ignore security/architecture checks, change wrong files, and break codebases. Developers must manually review and fix these failures.

CTH must solve these problems by providing an external, version-controlled harness that the agent cannot bypass, with clear roles, gates, logs, and human control.

## 3. Goals and Objectives
### 3.1 Goals
- Reduce production-breaking changes caused by agent edits.
- Provide persistent repo-wide context and impact analysis.
- Enforce architecture and security gates before critical transitions.
- Create a human-controlled team workflow inside the IDE.
- Enable full auditability and reproducibility of agent actions.
- Support multiple agent hosts with minimal adaptation.

### 3.2 Non-Goals
- Replacing the agent model or building a new agent framework.
- Providing enterprise-wide knowledge search across multiple organizations.
- Fully autonomous operation without human approval.
- A parallel multi-agent orchestrator with git worktrees in v1.

## 4. Stakeholders
- **Lead Developer / User:** Interacts with Cursor, approves phases, reviews artifacts.
- **Coding Agents:** Execute tasks under CTH constraints.
- **Future Researchers:** Use logs and metrics for evaluation.
- **Deployment/CI Systems:** May consume gate outputs for automated checks.

## 5. Functional Requirements

### 5.1 Team Workflow Engine
- **FR-01:** The harness must define at least these roles: Product Analyst, Architect, Backend Developer, Frontend Developer, Security Reviewer, QA Engineer, Release Manager.
- **FR-02:** The harness must support a sequential phase workflow: Requirements → Architecture → Implementation → Security Review → QA → Release.
- **FR-03:** Each phase must have a defined set of inputs, outputs, and acceptance criteria.
- **FR-04:** Phase transitions must require explicit human approval. The agent cannot self-advance.
- **FR-05:** The harness must maintain a state ledger (`.team/state.json`) that records current phase, completed phases, artifact status, and approval records.
- **FR-06:** The harness must maintain an append-only audit log (`.team/audit.jsonl`) for all significant events.
- **FR-07:** The agent must be able to query current state and request approval through a dedicated MCP interface.

### 5.2 Code Intelligence Layer
- **FR-08:** The harness must integrate a persistent structural code graph tool (initial candidate: `codebase-memory-mcp`).
- **FR-09:** The graph must provide exact symbol lookup, call path tracing, and impact analysis.
- **FR-10:** The harness must support Git-diff change detection (`detect_changes`) to map uncommitted changes to affected files/symbols.
- **FR-11:** The harness must provide version-aligned context snapshots that record the Git commit/hash at indexing time.
- **FR-12:** The harness must detect stale context and force re-index before high-risk edits.
- **FR-13:** The harness must optionally integrate an LSP-based semantic editing tool (candidate: `Serena`) for precise symbol rename and diagnostics. This may be added after initial validation.

### 5.3 Context & Memory System
- **FR-14:** The harness must generate and maintain `AGENTS.md` and `PROJECT_CONTEXT.md` from the current state and artifacts.
- **FR-15:** Context files must be version-controlled and injected into the agent’s context before each phase.
- **FR-16:** The context must include architecture summary, recent decisions, current role, notChecked areas, and constraints.
- **FR-17:** No separate retrieval-based memory MCP is required in v1 unless field evidence proves it is used.

### 5.4 Enforcement Layer
- **FR-18:** The harness must integrate with existing external gates: System Gate (architecture) and Sentinel (security).
- **FR-19:** Cursor rules must force the agent to call relevant tools before and after edits (pre-edit impact analysis, post-edit diff analysis).
- **FR-20:** The harness must not provide bypass flags (`force`, `skip`, `acknowledge`).
- **FR-21:** All PASS/FAIL/ERROR verdicts must come from external validators, not from agent self-assessment.

### 5.5 Logging and Observability
- **FR-22:** The harness must produce structured append-only logs in `.team/logs/` (JSONL).
- **FR-23:** Logs must record:
  - Phase transitions and human approvals.
  - Gate calls and results.
  - Code intelligence queries and responses.
  - Agent edits with before/after file hashes and Git diffs.
  - Tool calls, commands run, and scan outputs.
  - Context injection events and version snapshots.
- **FR-24:** Logs must be queryable for post-mortem analysis and research.

### 5.6 Host Integration
- **FR-25:** The harness must be installed and used inside Cursor (primary host).
- **FR-26:** The harness must be configurable through `.cursor/mcp.json` and `.cursor/rules/`.
- **FR-27:** The harness must provide commands for common user actions: `/team:status`, `/team:approve`, `/team:reject`, `/team:next`.

## 6. Non-Functional Requirements

### 6.1 Performance
- Graph queries should return results in under a few seconds for a medium-sized repo (50k–200k LOC).
- Context bundle generation should not block normal agent work.
- Logging should have minimal overhead.

### 6.2 Reliability
- The harness must be resumable after a Cursor crash or agent failure.
- Stale context detection must prevent incorrect impact analysis.
- The system must distinguish ERROR (tool broke) from FAIL (code/design issue).

### 6.3 Security
- All code indexing must remain local to the developer machine unless explicitly configured otherwise.
- No secrets should be stored in logs or context bundles.
- License compatibility must be respected (no PolyForm Noncommercial or similarly restrictive licenses).

### 6.4 Scalability
- The harness should handle a monorepo with backend, web, and mobile subprojects.
- The context and state system must remain practical as the repo grows (using incremental indexing and snapshot versions).

### 6.5 Maintainability
- The harness must be modular, with clear separation between workflow, intelligence, enforcement, and logging.
- Components must be swappable based on field evidence.

### 6.6 Usability
- The human should be able to understand the current phase and approve/reject with minimal friction.
- Documentation and commands must be clear and consistent.

### 6.7 Portability
- The harness core must be host-agnostic; Cursor-specific pieces must be isolated so Claude Code, Codex, and Antigravity can be added later.

## 7. User Stories and Workflows

### 7.1 Happy Path
1. As a lead developer, I open a new project in Cursor with the harness installed.
2. I provide a large PRD or product intent.
3. The harness begins in Requirements phase; the Product Analyst role summarizes and creates requirements artifacts.
4. I approve the requirements.
5. The Architect role creates architecture design and passes System Gate.
6. I approve the architecture.
7. The Backend and Frontend roles implement code, using code intelligence before edits and running Sentinel before commits.
8. I approve the implementation.
9. Security Reviewer runs Sentinel, produces a security report.
10. QA Engineer runs runtime and smoke tests.
11. I approve release readiness.
12. Release Manager finalizes, and I deploy.

### 7.2 Agent Tries to Skip Gate
- Agent attempts to commit without Sentinel check.
- Pre-commit hook or Cursor rule blocks/rejects; the agent is reminded to run Sentinel.
- If agent ignores, the human sees a failed state and rejects.

### 7.3 Context Lost
- Agent changes a shared type without checking impact.
- The harness forces a `detect_changes` call before edit or after diff.
- Affected files are identified and reviewed before tests.

## 8. Acceptance Criteria
- The harness can be installed in a fresh Cursor project and start in Requirements phase.
- All seven roles are defined and can be activated.
- Phase transitions require human approval and are recorded.
- Code intelligence tool responds with impact analysis on a test repo.
- Context bundle is generated and versioned.
- Logs capture all required events.
- The harness can be used to build a simple demo app end-to-end with no missing required gates.

## 9. Out of Scope for v1
- Parallel multi-agent orchestration with git worktrees.
- Separate memory MCP (TencentDB Agent Memory, Mem0, etc.).
- Fully autonomous phase advancement without human approval.
- Cross-repository enterprise knowledge graph.
- Voice or browser automation.
- Built-in CI/CD system (we integrate with existing tools).

## 10. Dependencies and Assumptions
- Cursor supports MCP servers and rules.
- `codebase-memory-mcp` works reliably for our target languages (TypeScript, Python, Dart).
- Sentinel and System Gate can be configured as MCP servers in Cursor.
- The human will operate inside Cursor and provide approvals.

## 11. Risks
- Cursor’s enforcement is soft; some agent actions may slip through.
- Static code graphs may miss dynamic or framework-generated relationships.
- Flutter/Dart semantic analysis may be incomplete.
- Overhead of multiple MCP calls could slow the agent.
- Tool maintenance may change and break integration.

## 12. Traceability
- Every requirement should trace to VISION.md core principles.
- Every design decision should reference DECISIONS.md and this document.

## 13. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
