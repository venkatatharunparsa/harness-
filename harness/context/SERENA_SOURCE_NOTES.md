# Source Notes: Serena

**Date:** 2026-08-20  
**Revision inspected:** `7fcbca7`  
**Package:** `serena-agent` 1.7.1.dev0 (latest release v1.7.0, 2026-08-09)  
**Method:** Local source/config/LICENSE inspection in Cursor workspace clone

## Executive Summary
Serena is a Python MCP agent toolkit providing IDE-like symbolic read/edit tools by spawning real language servers through SolidLSP. It is a local stdio MCP process, not a long-lived daemon with its own semantic graph.

- Strong: Drop-in MCP for Cursor; deep LSP fidelity for Python/TS/many languages; library-usable `SerenaAgent`
- Weak: No call-graph/hybrid-LSP index of its own; quality equals underlying language server
- Extensibility: YAML contexts/modes + LS path overrides; new tools/LS need code changes/fork
- Flutter: Dart LS only; no Flutter-specific tooling

## 1. Core Architecture
- Three packages in wheel: `serena`, `solidlsp`, `interprompt`
- Entry points: CLI `serena` / `serena-agent`; MCP via `start-mcp-server`; hooks `serena-hooks`
- Runtime model: local process, not daemon. Stdio child by default; optional HTTP/SSE; LS subprocesses
- User data in `~/.serena` or `$SERENA_HOME`

## 2. LSP Integration
- Uses SolidLSP to spawn LS subprocesses.
- Python default LS: `PyrightServer` via `uvx`; alternatives jedi, ty, pyrefly, basedpyright.
- TypeScript/JavaScript default: `typescript-language-server` + tsserver via npm.
- Dart: `DartLanguageServer`, auto-downloads Dart SDK zip pinned 3.7.1.
- Other languages: Java/JDTLS, Go/gopls, Rust/rust-analyzer, C# Roslyn/Omnisharp, etc.
- Lifecycle: parallel start, fail-fast on init; crash retry once; `restart_language_server` tool.
- Missing uv/uvx or node/npm → hard error.
- Overrides: `ls_path`, `ls_base_cmd`, `ls_specific_settings`.

## 3. MCP Tool Schemas
- Tool schemas derived from `apply()` signatures; no hand-written Pydantic models.
- Tool names: `FindSymbolTool` → `find_symbol`, etc.
- Returns: always string, JSON text or status; errors plain `ToolError`, no structured error envelope.
- No confidence fields.
- Limits: `default_max_tool_answer_chars = 150_000`; `tool_timeout = 240s`; no token hard-limit; no pagination.
- Soft limits: max_matches, depth, diagnostic line ranges.
- Key tools:
  - `find_symbol`: required `name_path_pattern`, optional `relative_path`, `depth`, `include_body`, `max_matches`, etc.
  - `find_referencing_symbols`: requires `name_path`, `relative_path`; returns refs with content around reference.
  - `rename_symbol`: requires `name_path`, `relative_path`, `new_name`; returns success status string.
  - `replace_symbol_body`, `insert_before_symbol`, `insert_after_symbol`: require `body`; return `"OK"`.
  - `safe_delete_symbol`: returns `"OK"` if no refs, otherwise refuses with referencing files/lines.
  - `get_diagnostics_for_file`: nested severity-map JSON, uses LSP severity ints, not confidence.
- Post-edit diagnostics exist in code but `ENABLE_DIAGNOSTICS = False` currently.

## 4. State and Persistence
- Delegates analysis to language servers; no custom call graph.
- Persists document symbol cache (pickle) for warm start.
- Project state: `.serena/project.yml`, `.serena/project.local.yml`, memories, cache, logs.
- Global state: `~/.serena/serena_config.yml`, contexts, modes, LS downloads.
- Memory: Markdown files in `.serena/memories/` and global `~/.serena/memories/global/`.
- Memory tools: write/read/delete/rename/edit; not a vector store.
- Contexts: YAML configs, fixed at MCP start; `ide` context recommended for Cursor.
- Project activation: `--project`, `--project-from-cwd`, or `activate_project` tool.

## 5. Cursor and Windows Integration
- Recommended Cursor config: `uvx --from git+https://github.com/oraios/serena serena start-mcp-server --context ide --project C:\path\to\workspace`.
- Stdio transport preferred. HTTP/SSE optional but watch issue #1890.
- `--project-from-cwd` walks ancestors for `.serena/project.yml` or `.git`; worktree-aware.
- Windows notes: line endings/core.autocrlf; logs at `%USERPROFILE%\.serena\logs`; C# needs pwsh; long ZIP paths handled; antivirus/file-lock deadlocks possible.
- Open issue: streamable-HTTP stalls under heavy/cold symbol calls.

## 6. Language Support Depth
- Broad language set documented in `docs/01-about/020_programming-languages.md`.
- Dart first-class via Dart Analysis Server, but no Flutter-specific backend; only `flutterOutline: False` init option.
- Monorepos: configure multiple `language_servers` in one project; optional indexed_folders.
- No dedicated pnpm/Next/FastAPI drivers; uses TS LS / Python LS / Dart LS only.

## 7. License and Maintenance
- MIT License, Copyright (c) 2025 Oraios AI.
- Latest release v1.7.0, active; ~28.3k stars; 64 open issues; top contributors opcode81, MischaPanch.
- Third-party components: multilspy-derived SolidLSP (MIT), Python MCP SDK, language servers themselves (separate licenses), FastMCP, Flask, pydantic, tiktoken, anthropic.

## 8. Extensibility
- Without forking: enable/disable tools, contexts/modes, override LS binary/args/version.
- Cannot add new MCP tools or language servers without modifying `serena.tools` / SolidLSP code.
- Can be used as Python library via `SerenaAgent`, but API may break; pin version.
- Hooks available via `serena-hooks`.

## Harness Integration Implications
- Complement, not replace, graph tools like codebase-memory-mcp.
- Use stdio + `--context ide --project <abs path>` on Windows.
- Budget for LS deps: Node/npm for TS, uv/uvx for Pyright, Dart SDK auto-download.
- Parse JSON strings; handle 150k char truncation and 240s timeouts; treat plain ToolError text.
- Flutter/Dart: navigation/edit only, no widget/pubspec/codegen awareness.
- Monorepos: configure multiple `language_servers` in `.serena/project.yml`.
- License: MIT for Serena; inventory spawned LS binaries for compliance.
