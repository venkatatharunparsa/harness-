# Research Proposal: A Discipline-Enforcing Harness for IDE-Native Coding Agents

**Status:** Draft for review  
**Author:** [Your name / team]  
**Date:** 2026-08-20

## Abstract
Modern IDE-native coding agents such as Cursor, Codex, Claude Code, and Antigravity can generate code quickly, but they often lack the context, memory, and discipline required to produce production-ready software. They lose context, ignore security gates, make changes without understanding impact, and often break codebases in ways that are difficult to trace. This project proposes a modular harness, built on top of existing open-source tools and evidence-driven principles, that transforms these agents into a structured engineering team with explicit roles, enforced gates, versioned codebase context, and human-controlled transitions. The harness will be evaluated on a real-world production SaaS/mobile application, with metrics for context effectiveness, impact analysis accuracy, gate compliance, and production reliability. The contribution is both a system artifact and a methodology for building reliable agent-assisted software development workflows.

## 1. Problem Statement
Despite advances in large language models, coding agents still exhibit several failure modes:
- They rely on shallow retrieval and lose critical context across sessions.
- They skip architecture and security checks unless externally forced.
- They make changes without understanding cross-file or cross-service impact.
- They produce code that may pass simple tests but fails in production due to unaddressed scalability, security, reliability, and observability requirements.
- Existing open-source tools are fragmented; no single system integrates them into a coherent, enforced workflow.

This project addresses the following research question: **How can we design a harness that reliably enforces production-grade software engineering discipline within an IDE-native coding agent, while maintaining developer control and practical usability?**

## 2. Research Objectives
1. Design a modular harness architecture that wraps an IDE agent with:
   - Team role definitions and phase transitions.
   - Versioned codebase context and structural code intelligence.
   - External evidence-based gates for architecture and security.
   - Human approval checkpoints.
2. Integrate best-of-breed open-source tools for code intelligence, context management, and enforcement, selected through rigorous evaluation.
3. Develop a measurement and logging framework to track agent behavior, gate compliance, context effectiveness, and impact accuracy.
4. Evaluate the harness on a real-world production-oriented project (web SaaS + mobile app) built from a large PRD.
5. Produce a set of field reports, benchmarks, and a publication-ready methodology that can guide future agent harness engineering.

## 3. Proposed System Overview
The harness consists of four layers:
- **Workflow Layer:** state machine for roles and phases, with human-approval gate transitions.
- **Intelligence Layer:** persistent code graph (e.g., `codebase-memory-mcp`) for impact analysis and versioned context, plus LSP-based semantic editing (e.g., `Serena`).
- **Enforcement Layer:** integration with existing evidence-based gates (System Gate for architecture, Sentinel for security) and host-specific hooks/rules.
- **Observability Layer:** append-only audit logs, context snapshots, and metrics dashboards.

All critical state is stored outside the model in version-controlled files (`.team/state.json`, `.team/audit.jsonl`, context bundles), ensuring reproducibility and human oversight.

## 4. Methodology
### 4.1 Tool Evaluation
Before integration, evaluate candidate open-source tools against a defined rubric:
- Licensing (must be commercially safe: MIT/Apache-2.0 preferred).
- Cursor MCP compatibility and setup complexity.
- Persistent structural graph capabilities.
- Incremental indexing freshness and correctness.
- Impact analysis precision/recall on real change sets.
- Language support for TypeScript, Python, and Dart/Flutter.
- Maintenance activity and bus factor.

Candidates: `codebase-memory-mcp`, `Serena`, `CodeGraph`, `GitNexus` (license-blocked), and others as needed.

### 4.2 System Development
Follow a phased plan:
1. Foundation docs (Vision, PRD, Architecture, Decisions).
2. Core team workflow state machine.
3. Code intelligence integration.
4. Context bundle generation.
5. Enforcement and hooks.
6. Dogfood evaluation and refinement.

### 4.3 Evaluation Plan
Use the harness to build a real prototype from a large PRD, with a web SaaS and mobile frontend. Record metrics:
- Gate call rate without explicit reminders.
- Human approval frequency and reasons.
- Impact analysis precision/recall on planned vs. actual affected files.
- Context snapshot freshness (stale vs. current graph).
- Number of production-breaking issues found by harness gates vs. missed.
- Token consumption per task compared to baseline.
- Developer time spent in manual review vs. baseline.

### 4.4 Data Collection
All events logged to `.team/logs/` in JSONL format. Context snapshots are committed. Field reports are maintained per phase. Data is version-controlled and auditable.

## 5. Expected Contributions
1. An open-source harness artifact that can be installed in Cursor (and later adapted to Claude Code, Codex, Antigravity).
2. A reproducible methodology for integrating code intelligence and enforcement into coding agents.
3. A benchmark dataset and evaluation protocol for code impact analysis tools in real projects.
4. Field evidence on the effectiveness of versioned context vs. retrieval-based memory for agents.
5. A publication-ready case study on building production software with an enforced agent harness.

## 6. Significance
This work bridges a critical gap between raw agent capability and production reliability. It addresses practical problems faced by developers using AI coding assistants, and contributes to the emerging field of agent harness engineering. The principles of evidence-based gates, versioned context, and human-in-the-loop approval are generalizable beyond Cursor to any autonomous coding workflow.

## 7. Risks and Limitations
- **Cursor’s soft enforcement limits:** Cursor cannot fully hard-stop certain actions; the harness must rely on external validators and human approval.
- **Code intelligence accuracy:** Tree-sitter-based graphs may miss dynamic or framework-generated relationships; we mitigate with LSP tools and tests.
- **Flutter/Dart semantics:** static analysis of Flutter is inherently limited; we pair with Dart Analyzer and tests.
- **Overhead:** too many MCP calls could slow the agent; we will measure and optimize.
- **Generalizability:** results may be specific to the chosen stack; we will document limits honestly.

## 8. Timeline (Tentative)
- Phase 0: Foundation docs and tool validation (2 weeks)
- Phase 1: Team workflow engine + context bundle (2–3 weeks)
- Phase 2: Code intelligence integration and rules (2 weeks)
- Phase 3: Dogfood on PRD prototype (3–4 weeks)
- Phase 4: Measurement, refinement, publication draft (2 weeks)

## 9. Expected Outcomes
- A working harness repository with documentation and reproducible setup.
- A published field report with metrics and lessons learned.
- A formal research paper draft targeting a systems / software engineering venue.

## 10. References
To be added as we consolidate sources (Constellation docs, research data, MCP tool docs, code intelligence deep dives).
