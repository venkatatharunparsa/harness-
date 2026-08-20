# Roadmap: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Purpose
This document defines the implementation roadmap for CTH. It breaks the work into phases, each with clear goals, deliverables, exit criteria, dependencies, and metrics. The roadmap is intentionally evidence-driven: we only advance when exit criteria are met, and we reassess if field evidence contradicts assumptions.

## 2. Guiding Principles
- No phase begins before its prerequisite docs and approvals are complete.
- Every phase produces measurable outputs and logs.
- We validate tools before building on top of them.
- We cut features if field evidence shows they are unused or harmful.
- The human approves each phase completion.

## 3. Phases Overview
| Phase | Name | Goal | Primary Deliverables |
|---|---|---|---|
| 0 | Foundation & Tool Validation | Validate Cursor + code intelligence stack, finalize docs | Working repo with docs, validated `codebase-memory-mcp`, Cursor rules |
| 1 | Core Team Workflow Engine | Implement state machine, roles, approvals, logging | `team-workflow` MCP v1, `.team/state.json`, audit log |
| 2 | Context Bundle System | Auto-generate `AGENTS.md` and `PROJECT_CONTEXT.md` | Context generation module, version alignment |
| 3 | Code Intelligence Integration | Integrate graph and LSP tools into workflow rules | Cursor rules, pre/post edit flows, `detect_changes` |
| 4 | Enforcement and Hooks | Wire external gates (System Gate, Sentinel) and Cursor commands | Gate validation, team commands, soft hooks |
| 5 | Dogfood Evaluation | Build a real prototype from a large PRD | Field report, metrics, issues, fixes |
| 6 | Refinement & Publication | Analyze results, improve system, prepare research | Updated harness, field reports, paper draft |

## 4. Phase 0: Foundation & Tool Validation
**Goal:** Confirm docs are complete and selected tools work in Cursor.

**Prerequisites:**
- Approved VISION, PROPOSAL, REQUIREMENTS, ARCHITECTURE, DESIGN, DECISIONS, RESEARCH, ROADMAP, VALIDATION_PLAN.
- Git repo initialized and pushed.

**Deliverables:**
- `codebase-memory-mcp` installed and configured in Cursor.
- Verification checklist completed: connection, indexing, `trace_call_path`, `detect_changes`, freshness.
- Cursor rules file `codebase-context.mdc` written.
- Tool evaluation notes in `context/TOOL_EVALUATION.md`.

**Exit Criteria:**
- Graph tool returns correct structure on a small test repo.
- `detect_changes` identifies changed files/symbols after a controlled edit.
- Cursor rules load and influence agent behavior (observed).
- Any tool limitation recorded.

**Metrics:**
- Indexing time for test repo.
- Query latency for `trace_call_path`.
- Precision/recall of `detect_changes` on 5 known changes.

## 5. Phase 1: Core Team Workflow Engine
**Goal:** Build the `team-workflow` MCP with state management, roles, and human approval.

**Prerequisites:**
- Phase 0 exit criteria met.

**Deliverables:**
- `team-workflow` MCP server (TypeScript) with tools:
  - `team_get_state`
  - `team_request_approval`
  - `team_record_artifact`
  - `team_validate_phase`
  - `team_advance`
  - `team_update_context` (stub for Phase 2)
- `.team/state.json` schema implemented.
- `.team/audit.jsonl` logger implemented.
- Basic unit tests.

**Exit Criteria:**
- State transitions work as designed.
- Agent cannot directly modify `.team/state.json` via MCP.
- Human approval required to move to next phase.
- Logs append correctly.

**Metrics:**
- Number of state corruption events (should be zero).
- Approval flow latency.
- Log completeness.

## 6. Phase 2: Context Bundle System
**Goal:** Automatically generate and version context files from state and artifacts.

**Prerequisites:**
- Phase 1 exit criteria met.

**Deliverables:**
- Context generation module in `team-workflow` MCP.
- Generates `AGENTS.md` and `PROJECT_CONTEXT.md`.
- Records `git_commit` and `graph_index_version` in context and state.
- Version alignment check before high-risk edits.

**Exit Criteria:**
- Context files reflect current phase/role/decisions.
- Context version is committed after each phase.
- Stale context detection works (mismatch triggers re-index warning).

**Metrics:**
- Context generation time.
- Staleness detection accuracy.
- Agent context adherence (qualitative for now).

## 7. Phase 3: Code Intelligence Integration
**Goal:** Enforce use of code intelligence tools before/after edits via Cursor rules and workflows.

**Prerequisites:**
- Phase 0 tool validation complete (can overlap with Phase 1/2).

**Deliverables:**
- Updated `.cursor/rules/codebase-context.mdc` to force graph queries before edits.
- Post-edit `detect_changes` call flow.
- Optional Serena integration if evidence demands precise refactoring (decision point).
- Tool call logging in `team-workflow` MCP.

**Exit Criteria:**
- On a test edit, agent queries graph before editing and runs `detect_changes` after (observed in logs).
- Impact analysis results are recorded.
- `team-workflow` MCP logs code intelligence queries.

**Metrics:**
- Graph tool call rate per edit.
- Number of times agent attempted edit without graph query.
- Impact analysis precision/recall on test cases.

## 8. Phase 4: Enforcement and Hooks
**Goal:** Integrate System Gate, Sentinel, and Cursor commands into the workflow.

**Prerequisites:**
- Phase 1 core workflow and Phase 3 code intelligence done.

**Deliverables:**
- `team_validate_phase` calls System Gate / Sentinel based on phase.
- Cursor commands `/team:status`, `/team:approve`, `/team:reject`, `/team:next`.
- Pre-commit/pre-push hooks (if possible) for Sentinel check.
- No bypass flags.

**Exit Criteria:**
- Architecture phase requires System Gate PASS to advance.
- Security Review requires Sentinel PASS to advance.
- Commands work from Cursor UI.
- Git hooks fire (if configured).

**Metrics:**
- Gate call rate per phase.
- Hook effectiveness (blocked actions).
- False positives/negatives from gates.

## 9. Phase 5: Dogfood Evaluation
**Goal:** Use the harness to build a real prototype from a large PRD.

**Prerequisites:**
- All core phases (0–4) complete and stable.

**Deliverables:**
- End-to-end run of the team workflow.
- Generated application prototype (web SaaS + mobile) from PRD.
- Filled `GREENFIELD-REPORT.md` (or equivalent in `context/`).
- Research diary entries with metrics.

**Exit Criteria:**
- Harness successfully guides agent through all phases.
- Human approvals recorded.
- Production-quality gates pass before release.
- List of improvements identified.

**Metrics:**
- Gate call rate without reminder.
- Human approval frequency/reasons.
- Impact analysis precision/recall on real changes.
- Number of production-breaking issues caught.
- Time saved vs manual review.

## 10. Phase 6: Refinement & Publication
**Goal:** Improve the system based on dogfood results; prepare research artifacts.

**Prerequisites:**
- Phase 5 completed.

**Deliverables:**
- Bug fixes and enhancements.
- Updated docs and decision log.
- Benchmark ledger.
- Draft research paper (if evidence supports).

**Exit Criteria:**
- Harness stability confirmed on second dogfood or extended run.
- Metrics show improvement over baseline.
- Publication-quality logs and field reports.

**Metrics:**
- Reduction in broken changes compared to baseline.
- User satisfaction (qualitative).
- Publication readiness score (if applicable).

## 11. Dependencies and Constraints
- Cursor supports MCP and rules (verified in Phase 0).
- `codebase-memory-mcp` MIT and local (verified).
- System Gate and Sentinel MCPs available or buildable.
- Human time for approvals and reviews.

## 12. Risks and Mitigations
| Risk | Impact | Mitigation |
|------|--------|------------|
| Code graph inaccurate for Flutter/Dart | Wrong impact analysis | Pair with Dart Analyzer/Serena; validate on real Flutter code |
| Cursor enforcement soft | Agent skips tools | Rely on human approval and external gates; measure and iterate |
| MCP overhead slows agent | Performance drop | Profile and optimize; selective tool usage |
| Tool maintenance changes | Breakage | Pin versions; modular swap |
| Docs drift from code | Confusion | Update docs at phase completion; traceability |

## 13. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
