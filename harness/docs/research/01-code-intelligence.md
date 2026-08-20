# Code Intelligence Research

**Category:** Code intelligence and repository graph tools  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file consolidates research on code intelligence tools: persistent code graphs, LSP-based semantic editing, structural analysis, and impact detection. These tools form the foundation of CTH's code context and precise editing capabilities.

## Repositories Evaluated

### 1. codebase-memory-mcp
- **Repo:** https://github.com/DeusData/codebase-memory-mcp
- **License:** MIT
- **Primary language:** C (87%), C++ (11%)
- **Maintainers / activity:** 610 commits, 46 contributors, 32 releases; latest v0.10.8
- **Status:** Adopt as primary graph engine

#### Key Findings (Source-Verified)
- Persistent SQLite-backed structural graph with nodes for files, functions, classes, routes, resources, etc.
- Tools: `trace_path` (`trace_call_path` alias), `detect_changes`, `get_architecture`, `search_graph`.
- SHA-256 content-hash incremental indexing; rename often triggers FULL rebuild.
- Watcher is git-poll based (5–60s), not fsnotify.
- Embedded hybrid LSP for TypeScript/JS/JSX/TSX, Python, Go, Java, Kotlin, Rust; no external language servers.
- Dart/Flutter is grammar-level only; missing widget composition, provider/riverpod/bloc wiring, part/part-of, platform channels.
- README node/edge catalog is stale; use `get_graph_schema` dynamically.
- `detect_changes` has no confidence/risk fields; `trace_path` supports optional risk labels and evidence.
- pnpm symlinks skipped; no workspace parsing; monorepo package discovery via manifest files.
- No public library API; MCP/CLI only.
- Windows issues: idle CPU, stale rendezvous, update locks, tree-sitter stack overflow.

#### CTH Usage
- Use as MCP binary, not library.
- Wrap with our `team-workflow` MCP to add confidence scoring, process aggregation, token capping, stale detection.
- Pair with Serena for Flutter/Dart semantic safety.
- Use `format:"json"` for all tool calls; handle pagination/cursors.

#### Evidence
- Source notes: `context/CODEBASE_MEMORY_MCP_SOURCE_NOTES.md`
- Source inspection report: docs/research/08-source-inspection-reports.md
- Perplexity deep dives: earlier research sessions.

---

### 2. Serena
- **Repo:** https://github.com/oraios/serena
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** v1.7.0, very active, ~28.3k stars
- **Status:** Adopt as precision semantic editing layer

#### Key Findings (Source-Verified)
- Python MCP toolkit using SolidLSP to spawn language servers.
- Python default LS: PyrightServer via uvx; alternatives jedi, ty, pyrefly, basedpyright.
- TypeScript/JavaScript via typescript-language-server; Dart via Dart Analysis Server.
- Tools return JSON strings/status; no confidence fields; no structured error envelope.
- No built-in pagination; max 150k chars output, 240s timeout.
- Language server lifecycle fail-fast on init; retry once on crash; tsserver crash detection.
- No Flutter-specific backend; Dart LS only.
- Project memory in `.serena/memories/` (Markdown, not vector store).
- Monorepo: configure multiple `language_servers` in `.serena/project.yml`.
- Extensible via YAML contexts/modes and LS overrides; new tools/LS need code changes.
- Can be embedded as Python library, but API may break.

#### CTH Usage
- Use as MCP binary for exact symbol operations and edits.
- Wrap with our MCP for output control, truncation, error mapping.
- Use `--context ide --project <abs path>` for Cursor.
- Pair with `codebase-memory-mcp` for graph-level impact before edits.
- Flutter workflow: Serena + Dart Analyzer + `flutter analyze` + `flutter test`.

#### Evidence
- Source notes: `context/SERENA_SOURCE_NOTES.md`
- Source inspection report: docs/research/08-source-inspection-reports.md
- Perplexity deep dives: earlier research sessions.

---

### 3. GitNexus
- **Repo:** https://github.com/abhigyanpatwari/GitNexus
- **License:** PolyForm Noncommercial 1.0.0
- **Primary language:** Node/TypeScript
- **Maintainers / activity:** Active, 1,068 commits, 324 releases
- **Status:** Exclude (commercial blocker); study concepts only

#### Key Findings
- Strong process tracing, confidence-ranked impact, cross-repo groups.
- Dedicated Cursor integration and MCP skills.
- License prevents commercial use without separate license.
- Incremental indexing was on roadmap; stale-index detection present.

#### CTH Usage
- Rebuild process tracing and confidence scoring on top of `codebase-memory-mcp`.
- Do not use code or adopt as dependency.

#### Evidence
- Research session data.
- License: https://github.com/abhigyanpatwari/GitNexus/blob/main/LICENSE

---

### 4. CodeGraph
- **Repo:** https://github.com/codegraph-ai/CodeGraph
- **License:** Apache-2.0
- **Primary language:** Rust
- **Maintainers / activity:** 143 commits, 2 contributors, 1 release; solo-maintained
- **Status:** Pilot candidate

#### Key Findings
- Persistent RocksDB graph with incremental indexing.
- Tools: `get_edit_context`, `pr_context`, `analyze_impact`, memory.
- TypeScript/JavaScript strongest; Python useful; Dart may require extra build.
- Solo maintainer and version/documentation fragmentation risks.
- Graph-only mode available.

#### CTH Usage
- Evaluate if `codebase-memory-mcp` gaps appear.
- Not default production choice.

#### Evidence
- Research session data.

---

### 5. repowise
- **Repo:** https://github.com/repowise-dev/repowise
- **License:** AGPL-3.0
- **Primary language:** Python
- **Maintainers / activity:** ~45k stars
- **Status:** Exclude (AGPL risk for proprietary embedding)

#### Key Findings
- Five intelligence layers: dependency graph, git history, docs, decisions, code health.
- Code health score predicts bug-prone files.
- AGPL license makes commercial embedding risky.

#### CTH Usage
- Borrow ideas for code health metrics; do not adopt as dependency.

#### Evidence
- Research session data.

---

### 6. code-review-graph
- **Repo:** https://github.com/tirth8205/code-review-graph
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** ~17.8k stars
- **Status:** Pilot candidate

#### Key Findings
- Blast-radius analysis focused; token efficient (38x–528x).
- 30 MCP tools; incremental updates under 2 seconds.
- Strong for impact analysis but not LSP-grade editing.

#### CTH Usage
- Consider if `codebase-memory-mcp` does not meet impact analysis needs.

#### Evidence
- Research session data.

---

### 7. Graphify
- **Repo:** https://github.com/Graphify-Labs/graphify
- **License:** Verify
- **Primary language:** Python
- **Status:** Pilot (low)

#### Key Findings
- Visual repository graph, portable artifacts, agent skills.
- Not a precise editing tool; more for onboarding/exploration.

#### CTH Usage
- Optional for visual exploration; not core.

#### Evidence
- Research session data.

---

### 8. CodeAlive MCP
- **Repo:** https://github.com/CodeAlive-AI/codealive-mcp
- **License:** Verify
- **Status:** Exclude (hosted/API, not fully local)

#### Key Findings
- Semantic search, exact grep, artifact fetching.
- Hosted/API-oriented; not ideal for local-first requirement.

#### CTH Usage
- Not suitable.

#### Evidence
- Research session data.

---

### 9. Aider repo map
- **Repo:** https://github.com/Aider-AI/aider
- **License:** Apache-2.0
- **Status:** Study (concept)

#### Key Findings
- Ranked repository map supplies relevant context to LLM.
- Not a persistent graph DB.

#### CTH Usage
- Borrow concept of ranked context selection.

#### Evidence
- Research session data.

---

## Comparison Table
| Feature | codebase-memory-mcp | Serena | GitNexus | CodeGraph |
|---|---|---|---|---|
| License | MIT | MIT | PolyForm NC | Apache-2.0 |
| Persistent graph | Yes | No | Yes | Yes |
| Impact/blast-radius | Yes | No | Yes | Yes |
| LSP semantic edits | Partial | Yes | No | No |
| Dart/Flutter support | Structural | Strong | Structural | Structural |
| Cursor MCP | Yes | Yes | Yes | Yes |
| Maintenance risk | Medium | Low | Low | High |
| Commercial safety | Safe | Safe | Unsafe | Safe |

## Decision Summary
- Primary graph engine: `codebase-memory-mcp`.
- Precision editing: `Serena`.
- Excluded: GitNexus (license), repowise (AGPL), CodeAlive (hosted).
- Pilot candidates: CodeGraph, code-review-graph.
- Borrow concepts from GitNexus process tracing and Aider repo map.

## Sources
- Detailed source notes: `context/CODEBASE_MEMORY_MCP_SOURCE_NOTES.md`, `context/SERENA_SOURCE_NOTES.md`
- Research sprints: `docs/CODE_INTELLIGENCE_RESEARCH.md`, `docs/GATES_RESEARCH.md`
- Perplexity results and Cursor source inspection reports.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial code intelligence category research |
