# Research: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Draft for review  
**Owner:** Human lead  
**Date:** 2026-08-20

## 1. Purpose
This document consolidates the research conducted to select the tools, patterns, and architecture for CTH. It summarizes the open-source repositories evaluated, their strengths and limitations, licensing constraints, and the rationale for our final choices. It serves as the evidence base for decisions recorded in `DECISIONS.md`.

## 2. Research Scope
We investigated four main areas:
- Code intelligence and repository graph tools.
- IDE-native agent integration (Cursor, MCP).
- Context and memory systems.
- Agent harness reference architectures.

## 3. Executive Summary
After reviewing multiple open-source tools, we selected:
- **Primary code graph:** `codebase-memory-mcp` (MIT, local, persistent, `detect_changes`).
- **Precise semantic editing:** `Serena` (MIT, LSP-based, deferred until needed).
- **Excluded:** `GitNexus` due to PolyForm Noncommercial license.
- **Pilot candidate:** `CodeGraph` (Apache-2.0, but solo-maintainer risk).
- **Context strategy:** Git-backed Markdown, no separate retrieval MCP in v1.
- **Enforcement:** External gates (System Gate, Sentinel) + Cursor rules/commands.

## 4. Code Intelligence Tools Evaluation

### 4.1 codebase-memory-mcp
- **Repo:** `DeusData/codebase-memory-mcp`
- **License:** MIT
- **Language:** C (87%), C++ (11%)
- **Key capabilities:**
  - Persistent SQLite-backed structural graph.
  - `search_graph`, `trace_call_path`, `detect_changes`, `get_architecture`, `get_code_snippet`.
  - Incremental content-hash indexing.
  - 155 Tree-sitter languages; hybrid LSP for TypeScript/JavaScript/JSX/TSX/Python, etc.
  - Local-first, single binary, MCP stdio.
  - Cross-service HTTP/route detection.
- **Strengths for our stack:**
  - Strong for TypeScript/Node and Python/FastAPI.
  - Git-diff impact analysis directly addresses our need.
  - MIT license suitable for commercial SaaS.
- **Limitations:**
  - Dart/Flutter only structural, not semantically reliable.
  - Static graph may miss dynamic/framework-generated relationships.
  - No independent precision/recall benchmark; must test on our repos.
- **Decision:** Adopt as primary code graph engine.

### 4.2 Serena
- **Repo:** `oraios/serena`
- **License:** MIT
- **Language:** Python (focus on LSP tools)
- **Key capabilities:**
  - LSP-backed symbol lookup, references, implementations, rename, symbol-body replacement, diagnostics.
  - Supports TypeScript/JavaScript, Python, Dart via language servers.
  - Works with Cursor via MCP stdio.
- **Strengths for our stack:**
  - Precise semantic editing and refactoring.
  - Strong Dart/Flutter support through Dart language server (better than structural-only graph).
- **Limitations:**
  - Not a repository-wide graph or blast-radius tool.
  - Requires language server configuration.
- **Decision:** Add later when precise refactoring needs arise; not part of initial core.

### 4.3 CodeGraph
- **Repo:** `codegraph-ai/CodeGraph`
- **License:** Apache-2.0
- **Language:** Rust
- **Key capabilities:**
  - Persistent RocksDB graph with incremental hashing.
  - `get_edit_context`, `pr_context`, `analyze_impact`, memory.
  - Graph-only mode.
- **Strengths:**
  - Integrated edit context and PR impact.
- **Limitations:**
  - Solo maintainer, 143 commits, 2 contributors, 1 release.
  - Documentation fragmentation (stdio vs HTTP modes).
  - Dart may require extra-language build.
- **Decision:** Treat as pilot candidate; not default production yet.

### 4.4 GitNexus
- **Repo:** `abhigyanpatwari/GitNexus`
- **License:** PolyForm Noncommercial 1.0.0
- **Key capabilities:** Strong process/execution flow, impact, cross-repo groups.
- **Why excluded:** License not safe for commercial SaaS development without separate commercial license.
- **Decision:** Exclude from core; may evaluate with license if needed.

### 4.5 Comparison Summary
| Feature | codebase-memory-mcp | Serena | CodeGraph | GitNexus |
|---|---|---|---|---|
| License | MIT | MIT | Apache-2.0 | PolyForm NC |
| Persistent graph | Yes | No | Yes | Yes |
| Impact/blast-radius | Yes | No | Yes | Yes |
| LSP semantic edits | Partial | Yes | No | No |
| Dart/Flutter support | Structural | Strong | Structural | Structural |
| Cursor MCP | Yes | Yes | Yes | Yes |
| Maintenance risk | Medium | Low | High | Low |
| Commercial safety | Safe | Safe | Safe | Unsafe |

## 5. Cursor Native Capabilities and Gaps
Research on Cursor native features shows:
- **Strengths:**
  - Semantic codebase indexing with incremental Merkle tree updates.
  - Instant Grep (exact/regex search).
  - Multi-root workspace indexing.
  - Rules and commands for persistent instructions.
  - MCP support (project and global).
- **Gaps:**
  - No public persistent, queryable code graph API.
  - No first-class blast-radius or Git-diff-to-symbol mapping.
  - Native search is retrieval, not relationship analysis.
  - Enforcement hooks are soft (stop limited, shell guard only).
- **Implication:** We need external graph MCP and external validators.

## 6. Context and Memory Systems

### 6.1 TencentDB Agent Memory
- **Repo:** `TencentCloud/TencentDB-Agent-Memory`
- **License:** MIT
- **Capabilities:** L0–L3 memory pyramid, Skills, Wiki, CodeGraph, Memory Hub.
- **Assessment:** Powerful but overkill for single-project harness. We borrow the layered context idea.

### 6.2 Mem0
- Existing in Constellation as optional local SQLite. Not a contract; not used in v1.

### 6.3 Git-backed Context Approach (Chosen)
- Use `AGENTS.md` and `PROJECT_CONTEXT.md` generated after each phase.
- Version-controlled, preloaded into agent context.
- Evidence from Constellation field data: retrieval MCP ignored; rules consumed.
- No separate memory MCP in v1.

## 7. Agent Harness Reference Architectures
We studied:
- **DeepSeek Harness:** plugin architecture, append-only session log, resume/fork. Borrow concepts of modular plugins and event logging.
- **DeerFlow/Hermes/OpenClaw:** sub-agent orchestration, skills, memory. Borrow role/artifact patterns, but not adopt as host.
- **Coding orchestrators (Composio, Conductor):** task decomposition, review loops. Borrow phase review pattern.
- **OpenShell/Agent Governance:** policy enforcement, audit. Borrow boundary concepts.

## 8. Protocols and Integration
- **MCP:** Standard for tool integration in Cursor. Our `team-workflow` MCP will wrap other MCPs.
- **A2A:** Not required for v1; future multi-agent.
- **Agent Skills spec / AGENTS.md:** Use for role definitions and context bundle format.

## 9. Final Tool Selection and Rationale
| Component | Choice | License | Rationale |
|---|---|---|---|
| Code graph | `codebase-memory-mcp` | MIT | Local, persistent, impact analysis, TypeScript/Python strong |
| Semantic editing | `Serena` | MIT | LSP precision, deferred until needed |
| Architecture gate | System Gate | Existing | Evidence-based architecture readiness |
| Security gate | Sentinel | Existing | Wraps scanners, ship readiness |
| Context | Git-backed Markdown | N/A | Preloaded, versioned, ignored-free |
| Orchestration | Custom `team-workflow` MCP | To be built | Single interface, enforces workflow |
| Logging | JSONL in `.team/logs/` | N/A | Append-only, research-friendly |

## 10. Open Research Questions
- How to make Cursor stop actually hard without soft loop_limit?
- How to ensure agent calls graph tools reliably?
- Best way to capture token usage and tool call metrics from Cursor?
- How to handle monorepo multi-root indexing with codebase-memory-mcp?
- Should Serena be added sooner for Flutter refactoring?
- How to version and prune graph snapshots without bloating repo?

## 11. References
- [codebase-memory-mcp GitHub](https://github.com/DeusData/codebase-memory-mcp)
- [Serena GitHub](https://github.com/oraios/serena)
- [CodeGraph GitHub](https://github.com/codegraph-ai/CodeGraph)
- [GitNexus GitHub](https://github.com/abhigyanpatwari/GitNexus)
- [Cursor MCP docs](https://cursor.com/docs/mcp)
- [Cursor rules docs](https://cursor.com/docs/rules)
- [TencentDB Agent Memory](https://github.com/TencentCloud/TencentDB-Agent-Memory)
- Constellation docs shared in earlier research sessions.

## 12. Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial draft |
