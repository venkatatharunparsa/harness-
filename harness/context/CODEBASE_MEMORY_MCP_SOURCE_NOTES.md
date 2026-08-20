# Source Notes: codebase-memory-mcp

**Date:** 2026-08-20  
**Revision inspected:** `dfe67cc771aa3783b87cd7a139326535fcba6ee3`  
**Release:** v0.10.8  
**Method:** Local source/config/LICENSE inspection in Cursor workspace clone

## Executive Summary
`codebase-memory-mcp` is a native C MCP server with an in-process Tree-sitter + embedded hybrid LSP pipeline, SQLite graph store, and git-poll watcher. It is strong as a stdio MCP + CLI, but weak as a library/API. Extension without forking is limited to extension→built-in language remaps, ignore rules, and env/config knobs.

## Critical Corrections from Source
| Previous assumption | Source-verified reality | CTH impact |
|---|---|---|
| README node/edge catalog is authoritative | README is incomplete/stale; use `get_graph_schema` or source | Our wrapper must query schema dynamically |
| `detect_changes` provides risk classification | It does NOT; only `trace_path` has optional risk labels | Our wrapper must add confidence/risk scoring |
| Library/API available | No public library surface; only MCP binary + CLI | We must wrap it, not embed it |
| Incremental indexing handles renames | Renames often trigger FULL rebuild | Plan reindexes before high-risk edits |
| File watcher uses fsnotify | Git-poll watcher only; 5–60s interval | Staleness window exists; handle in wrapper |
| Dart/Flutter has semantic resolution | Dart is grammar-level only; no part/codegen edges | Serena/Dart LSP is mandatory |
| pnpm monorepo packages are discovered | Manifest scanning only; symlinks skipped; no workspace parsing | pnpm cross-package edges will be incomplete |

## 1. Graph Storage and Schema
- SQLite tables: `projects`, `file_hashes`, `nodes`, `edges`, `project_summaries`, `lsp_surface`, `index_coverage`, `index_coverage_meta`, FTS5 `nodes_fts`, `store_meta`.
- Node columns: `id`, `project`, `label`, `name`, `qualified_name`, `file_path`, `start_line`, `end_line`, `properties`.
- Node labels include: Project, Package, Folder, File, Module, Class, Function, Method, Interface, Enum, Type, Route, Resource, plus many emitted labels not in README.
- Edge columns: `id`, `project`, `source_id`, `target_id`, `type`, `properties`, plus generated `url_path_gen` and `local_name_gen`.
- Canonical edge types: CALLS, CALL_REFERENCE, CONFIGURES, CONTAINS_FILE, CONTAINS_FOLDER, DATA_FLOWS, DECORATES, DEFINES, DEFINES_METHOD, DEPENDS_ON, FILE_CHANGES_WITH, GRAPHQL_CALLS, GRPC_CALLS, HANDLES, HTTP_CALLS, IMPLEMENTS, IMPORTS, INHERITS, INFRA_MAPS, OVERRIDE, SEMANTICALLY_RELATED, SIMILAR_TO, TESTS_FILE, TESTS, TRPC_CALLS, USAGE, ASYNC_CALLS, plus EMITS, LISTENS_ON, THROWS/RAISES, READS/WRITES, HAS_BRANCH.
- Cross-repo edges: CROSS_HTTP_CALLS, CROSS_ASYNC_CALLS, CROSS_CHANNEL, CROSS_GRPC_CALLS, CROSS_GRAPHQL_CALLS, CROSS_TRPC_CALLS.
- FKs on file_hashes, nodes, edges, lsp_surface, index_coverage_meta to projects; edges source/target to nodes with CASCADE.
- No ALTER migration chain; rebuild on schema mismatch.

## 2. Incremental Indexing
- Content hash: SHA-256. Race guard via mtime_ns + size; fail closed on second race.
- Hashes stored in SQLite `file_hashes`.
- Delete: purge if stat ENOENT; preserve if uncertain. Rename: no first-class rename; old path deleted, new file often causes FULL rebuild.
- Watcher: git HEAD + dirty FNV-1a signature over git status; base interval 5s, adaptive to 60s. No fsnotify.
- Re-index triggers: watcher, MCP index_repository, session auto-index, CLI index.
- detect_changes does NOT reindex.
- Risks: mid-reindex edits not absorbed; closure decline causes FULL; Windows mtime coarse.

## 3. Hybrid LSP Resolution
- Embedded C type resolvers under internal/cbm/lsp; does NOT spawn external language servers.
- Documented 9 families: Go, Python, TypeScript/JS/JSX/TSX, PHP, C#, C/C++/CUDA, Java, Kotlin, Rust. Perl also per-file.
- TypeScript: js_mode, jsx_mode, dts_mode; resolved_calls with strategy e.g. lsp_ts. Disable via CBM_LSP_DISABLED.
- Python: binder + type eval + attribute/MRO + call resolver; depth/budget caps degrade to unknown/skip.
- Failure: unresolved diagnostics strategy lsp_unresolved confidence=0.0; confidence floor 0.6; unresolved never wins.

## 4. Configuration and Extension Points
- Env vars: CBM_CACHE_DIR, CBM_ALLOWED_ROOT, CBM_RUNTIME_DIR, CBM_DIAGNOSTICS, CBM_LOG_LEVEL, CBM_WORKERS, CBM_MEM_BUDGET_MB, CBM_MAX_FILE_BYTES, CBM_MCP_MAX_DEPTH, CBM_CYPHER_MAX_DEPTH, etc.
- Config files: global `config.json` for extra_extensions; project `.codebase-memory.json`; CLI config DB in cache dir.
- `.cbmignore`: gitignore syntax, loaded at repo root, last-match-wins; cannot un-skip .git, node_modules, .worktrees, .claude-worktrees.
- Extension without forking: remap extensions to built-in languages only. No new languages/grammars/edge types. No public library API.

## 5. MCP Tool Output Schemas
- Advertised tool is `trace_path`; legacy alias `trace_call_path` accepted.
- No declared outputSchema; default text/TOON, optional format:"json".
- Shared envelope: content + structuredContent + isError.
- search_graph JSON: total, count, cols, groups, has_more; pagination via offset/limit.
- trace_path JSON: function, direction, mode, callees_total, callees, callers_total, callers, truncated, next_cursor. Optional risk_labels hop→CRITICAL/HIGH/MEDIUM/LOW. Optional include_evidence adds strategy + confidence.
- get_architecture: aspect-driven sections; compact summary by default; clusters have cohesion; no risk/confidence.
- detect_changes JSON: base, merge_base, changed_files, seed_symbols, impacted_total, impacted_shown, impacted[], impacted_modules[], truncated, hint. NO confidence/risk/severity fields.

## 6. Windows and Cursor Integration
- install.ps1 downloads release zip to `%LOCALAPPDATA%\Programs\codebase-memory-mcp`; allowlists archive entries; retires running binary via rename.
- Cursor detected via ~/.cursor; writes global `%USERPROFILE%\.cursor\mcp.json`.
- MCP command uses absolute install binary path.
- Known issues: idle MCP clients burn CPU, stale rendezvous blocks update, upgrade doesn't preserve settings, SIGSEGV on large projects, tree-sitter stack overflow on Windows.

## 7. Monorepo and Flutter Handling
- Package discovery: independent FS walk for manifest files; does NOT parse pnpm workspaces/yarn workspaces/turbo.
- Dart: pubspec.yaml name → `<dir>/lib`; `.dart_tool` always skipped; `build` skipped in fast/moderate only.
- part/part-of: grammar nodes exist but extractor only handles import_or_export; no part edges.
- Generated `.g.dart`/freezed: no dedicated skip in FULL.
- pnpm symlinks/reparse points always skipped; no pnpm-specific linker.

## 8. License and Maintenance
- MIT License confirmed.
- Latest release v0.10.8, rapid cadence; ~383 open issues; active.
- Third-party components: Tree-sitter grammars mostly MIT/CC0/Apache/ISC; SQLite public domain; mimalloc/yyjson MIT; xxHash BSD-2; Zstandard BSD-3; nomic embeddings vendored.

## Harness Integration Implications
- Treat as MCP binary + daemon, not library.
- Wire `trace_path` with format:"json"; handle pagination/cursors.
- Do not rely on README catalogs; query get_graph_schema.
- Expect FULL rebuilds on renames/new files/git-context changes.
- Hybrid LSP is internal; no external language server installation.
- Monorepo works via nested manifests, not workspace metadata; pnpm link farms invisible.
- Flutter/Dart grammar-level only; pair with Serena + Dart Analyzer.
- Windows: plan absolute MCP paths, update lock, CBM_RUNTIME_DIR, idle CPU.
