# Source Inspection Reports

**Category:** Direct source-inspection evidence for critical tools  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file links to detailed source-inspection notes created by inspecting repository clones inside Cursor. These reports are evidence-grade and correct assumptions from earlier high-level research.

## Source Inspection Reports

### 1. codebase-memory-mcp
- **Repo:** https://github.com/DeusData/codebase-memory-mcp
- **Revision inspected:** `dfe67cc771aa3783b87cd7a139326535fcba6ee3`
- **Release:** v0.10.8
- **Inspection method:** Cursor source clone, local file analysis
- **Notes file:** `context/CODEBASE_MEMORY_MCP_SOURCE_NOTES.md`

#### Summary of source-verified corrections
| Previous assumption | Source-verified reality | CTH impact |
|---|---|---|
| README node/edge catalog authoritative | README incomplete/stale; use `get_graph_schema` dynamically | Wrapper must query schema, not assume edges |
| `detect_changes` provides risk classification | It does not; only `trace_path` has optional risk labels | Wrapper must add confidence/risk scoring |
| Library/API available | No public library surface; MCP/CLI only | We must wrap it, not embed it |
| Renames handled incrementally | Renames often trigger FULL rebuild | Plan reindexes before high-risk edits |
| Watcher uses fsnotify | Git-poll watcher only, 5–60s | Staleness window exists; handle in wrapper |
| Dart semantic resolution | Dart grammar-level only; no part/codegen edges | Serena/Dart LSP mandatory |
| pnpm packages discovered | Manifest scanning only; symlinks skipped; no workspace parsing | pnpm cross-package edges incomplete |

#### Key implementation notes
- Use `trace_path` with `format:"json"`, not only `trace_call_path`.
- Handle pagination: `has_more`, `offset`, `next_cursor`, `impacted_shown`.
- Expect FULL rebuilds on renames, new files, git-context changes.
- Hybrid LSP is internal; no external language server installation required.
- Monorepo works via nested manifests, not workspace metadata.
- Flutter/Dart is grammar-level only; pair with Serena + Dart Analyzer.
- Windows: absolute MCP paths, update lock, `CBM_RUNTIME_DIR`, idle CPU issues.

---

### 2. Serena
- **Repo:** https://github.com/oraios/serena
- **Revision inspected:** `7fcbca7`
- **Release:** v1.7.0 (2026-08-09); dev `1.7.1.dev0`
- **Inspection method:** Cursor source clone, local file analysis
- **Notes file:** `context/SERENA_SOURCE_NOTES.md`

#### Summary of source-verified corrections
| Previous assumption | Source-verified reality | CTH impact |
|---|---|---|
| Python default LS is pylsp | Python default LS is Pyright via uvx | Use Pyright for FastAPI |
| multilspy external dependency | SolidLSP is packaged inside Serena | No separate multilspy management |
| Graceful LS lifecycle | Fail-fast on init; retry once after crash | Wrapper must detect and surface ERROR |
| Tools expose confidence/structured errors | Return plain JSON strings/status; no confidence | Wrapper must add its own confidence/error mapping |
| Pagination/output limits | No built-in pagination; max 150k chars, 240s timeout | Wrapper must cap/truncate outputs |
| Deep Flutter support | Dart LS only; no Flutter-specific backend | Flutter still needs flutter analyze/test |
| pnpm/Next/FastAPI special handling | No dedicated drivers; LS only | Configure multiple language_servers manually |

#### Key implementation notes
- Use stdio MCP with `--context ide --project <abs path>`.
- Budget for LS deps: Node/npm for TS, uv/uvx for Pyright, Dart SDK auto-download.
- Parse JSON strings; handle 150k char truncation and 240s timeouts.
- No confidence fields; errors plain ToolError text.
- Flutter/Dart navigation/edit only; no widget/pubspec/codegen awareness.
- Monorepo: multiple `language_servers` in `.serena/project.yml`.
- License: MIT for Serena; inventory spawned LS binaries separately.

---

## How Source Inspection Was Performed

For each critical tool:
1. Cloned the repository locally with `--depth 1`.
2. Opened the clone in Cursor as an isolated workspace.
3. Instructed Cursor Agent with a structured deep-source investigation prompt.
4. Cursor inspected source files, config files, LICENSE, and recent issues.
5. Cursor produced a technical report with exact file paths and line references.
6. Findings were recorded in `context/*_SOURCE_NOTES.md` and linked here.

This method gives us primary-source confidence, not marketing claims.

## Remaining Source Inspections
- Constellation System Gate — pending.
- Constellation Sentinel — pending.
- Other tools as needed.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial source inspection reports index |
