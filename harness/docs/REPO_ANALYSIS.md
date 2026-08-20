# Repository Analysis: Constellation Team Harness (CTH)

**Version:** 0.2  
**Status:** Living document  
**Owner:** Human lead  
**Date:** 2026-08-20

## Purpose
This document records deep analysis of each open-source repository we evaluate. The goal is to extract usable patterns, components, and workflows while respecting licenses. Every claim must be backed by evidence or marked as unverified.

## Template for Each Repo

### Repo: <name>
- **URL:**
- **License:**
- **Primary language:**
- **Maintainers / activity:**
- **Status:** (adopt / study / exclude / pilot)

#### 1. What the repo does well
#### 2. Specific ideas/workflows worth borrowing
#### 3. Reusable components (if any)
#### 4. Limitations and risks
#### 5. Validation needed before adopting
#### 6. Decision

---

## Analysis 1: OpenHands

- **URL:** https://github.com/All-Hands-AI/OpenHands
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** All Hands AI, very active, 76k+ stars
- **Status:** Study (reference architecture)

### 1. What the repo does well
OpenHands provides a production-quality agent platform with:
- Event-sourced state management (`ConversationState` + append-only `EventLog`).
- Model-agnostic LLM abstraction via LiteLLM.
- Type-safe tool system with Action/Execution/Observation pattern.
- SecurityAnalyzer risk assessment + ConfirmationPolicy for user approval.
- SecretRegistry with masking, encryption, and rotation.
- Opt-in sandboxing via workspace abstraction.
- MCP as first-class SDK tools.
- Context condensation to manage LLM context window.
- Multi-agent delegation.

### 2. Specific ideas/workflows worth borrowing
- **Event-sourced state:** Our `.team/state.json` + `.team/audit.jsonl` should follow an append-only event log with snapshot recovery. This gives deterministic replay and crash recovery.
- **Risk-based confirmation:** Implement a deterministic risk assessor before shell/file edits. Low risk auto-allow; medium/high require human approval. This mirrors our `preToolUse` hook plus approval state machine.
- **Secret management:** Extend Sentinel with secret lifecycle management (masking, rotation, audit) rather than only secret detection.
- **Condensation:** Our context bundle generation should include summarization of old decisions/events to keep `AGENTS.md` and `PROJECT_CONTEXT.md` compact.
- **Workspace abstraction:** In future, isolate agent execution via a workspace interface, but keep local-first for v1.
- **MCP first-class integration:** Confirms our design to use MCP as the primary interface for all harness tools.

### 3. Reusable components
- None directly. We borrow patterns, not code.

### 4. Limitations and risks
- Context window degradation on long tasks.
- Security vulnerabilities (CVEs) due to arbitrary code execution.
- Benchmark contamination concerns.
- Single-user self-hosted design lacks built-in auth (Enterprise adds it).
- Large codebase, not a drop-in component.

### 5. Validation needed before adopting
- None for direct adoption; patterns are validated by OpenHands production use.

### 6. Decision
- **Study** for architecture patterns. Do not adopt as a dependency.

---

## Analysis 2: codebase-memory-mcp

- **URL:** https://github.com/DeusData/codebase-memory-mcp
- **License:** MIT
- **Primary language:** C (87%), C++ (11%)
- **Maintainers / activity:** 610 commits, 46 contributors, 32 releases
- **Status:** Adopt / wrap as primary graph engine

### 1. What the repo does well
- Persistent SQLite-backed structural graph with nodes for files, functions, classes, routes, resources, etc.
- Tools: `search_graph`, `trace_call_path`, `detect_changes`, `get_architecture`, `get_code_snippet`, `manage_adr`.
- Incremental content-hash indexing with background watcher.
- 155 languages via Tree-sitter; hybrid LSP for TypeScript/JavaScript/JSX/TSX/Python, etc.
- Cross-service HTTP route detection, gRPC/GraphQL/tRPC.
- CLI mode and stdio MCP mode.
- Local-first, no cloud, MIT license.

### 2. Specific ideas/workflows worth borrowing
- **Persistent code graph:** Store structural relationships across sessions.
- **Impact analysis:** `detect_changes` maps git diff to affected symbols with risk classification.
- **Call path tracing:** `trace_call_path` provides inbound/outbound BFS.
- **Community detection:** Louvain clustering in `get_architecture` can support process grouping.
- **Monorepo root indexing:** manifest scanning discovers packages; use `.cbmignore` for exclusions.
- **Token-efficient queries:** Use `LIMIT`, depth caps, and schema-first queries.

### 3. Reusable components
- `codebase-memory-mcp` itself as an MCP server. We wrap it with our `team-workflow` MCP to add confidence scoring, process aggregation, and token capping.

### 4. Limitations and risks
- Dart/Flutter support is structural only; misses widget composition, provider/riverpod/bloc wiring, generated files, part/part-of, platform channels.
- Confidence scores computed internally but not exposed; we must implement own scoring.
- No built-in output truncation/pagination; wrapper must cap.
- Symlinks always skipped; pnpm workspace cross-package resolution may be incomplete.
- Tree-sitter static parsing misses dynamic/framework-generated behavior.

### 5. Validation needed before adopting
- Verify Dart parsing and symbol extraction on our Flutter code.
- Test `detect_changes` precision/recall on known changes.
- Validate pnpm monorepo cross-package edges.
- Measure token output sizes on medium repo.
- Confirm Windows compatibility and Cursor MCP startup.

### 6. Decision
- **Adopt** as primary graph engine; build wrapper MCP for enrichment.
