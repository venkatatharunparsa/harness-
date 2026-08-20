# Repository Analysis: Constellation Team Harness (CTH)

**Version:** 0.3  
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
- **Event-sourced state:** Our `.team/state.json` + `.team/audit.jsonl` should follow an append-only event log with snapshot recovery.
- **Risk-based confirmation:** Implement a deterministic risk assessor before shell/file edits. Low risk auto-allow; medium/high require human approval.
- **Secret management:** Extend Sentinel with secret lifecycle management (masking, rotation, audit).
- **Condensation:** Our context bundle generation should include summarization of old decisions/events.
- **Workspace abstraction:** In future, isolate agent execution via a workspace interface, but keep local-first for v1.
- **MCP first-class integration:** Confirms our design to use MCP as the primary interface.

### 3. Reusable components
- None directly. We borrow patterns, not code.

### 4. Limitations and risks
- Context window degradation on long tasks.
- Security vulnerabilities due to arbitrary code execution.
- Benchmark contamination concerns.
- Single-user self-hosted design lacks built-in auth (Enterprise adds it).
- Large codebase, not a drop-in component.

### 5. Validation needed before adopting
- None for direct adoption; patterns are validated by OpenHands production use.

### 6. Decision
- **Study** for architecture patterns. Do not adopt as a dependency.

---

## Analysis 2: codebase-memory-mcp (Source-Verified)

- **URL:** https://github.com/DeusData/codebase-memory-mcp
- **License:** MIT
- **Primary language:** C (87%), C++ (11%)
- **Maintainers / activity:** 610 commits, 46 contributors, 32 releases; latest v0.10.8
- **Status:** Adopt as primary graph engine; wrap with our MCP

### 1. What the repo does well
- Persistent SQLite graph with nodes/edges for files, functions, classes, routes, resources, etc.
- Tools: `trace_path` (`trace_call_path` alias), `detect_changes`, `get_architecture`, `search_graph`.
- SHA-256 content-hash incremental indexing.
- Embedded hybrid LSP for TypeScript/JS/JSX/TSX, Python, Go, Java, Kotlin, Rust, etc. No external language servers.
- CLI mode and stdio MCP mode.
- Local-first, MIT license.

### 2. Specific ideas/workflows worth borrowing
- **Persistent code graph:** Store structural relationships across sessions.
- **Impact analysis:** `detect_changes` maps git diff to affected symbols, but no confidence/risk fields.
- **Call path tracing:** `trace_path` provides inbound/outbound BFS with optional risk labels and evidence.
- **Community detection:** `get_architecture` includes Louvain clusters.
- **Monorepo root indexing:** manifest scanning discovers packages; use `.cbmignore`.
- **Token-efficient queries:** Use LIMIT, depth caps, pagination, schema-first queries.

### 3. Reusable components
- `codebase-memory-mcp` binary as MCP server. We wrap it with our `team-workflow` MCP to add confidence scoring, process aggregation, and token capping.

### 4. Limitations and risks
- Dart/Flutter is grammar-level only; missing widget composition, provider/riverpod/bloc wiring, part/part-of, platform channels.
- README node/edge catalog is stale; use `get_graph_schema` dynamically.
- `detect_changes` has no confidence/risk/severity; our wrapper must add it.
- Renames often trigger FULL rebuilds.
- Watcher is git-poll only; staleness window exists.
- pnpm symlinks are skipped; cross-package edges incomplete.
- No public library API; binary/MCP only.
- Windows issues: idle CPU, stale rendezvous, update locks, tree-sitter stack overflow.

### 5. Validation needed before adopting
- Verify Dart parsing on our Flutter code.
- Test `detect_changes` precision/recall on known changes.
- Validate pnpm monorepo cross-package edges.
- Measure token output sizes.
- Confirm Windows Cursor MCP startup and absolute paths.
- Measure staleness window from git-poll watcher.

### 6. Decision
- **Adopt** as primary graph engine. Build wrapper MCP for enrichment and risk/confidence additions. Pair with Serena for Flutter/Dart semantic safety.
