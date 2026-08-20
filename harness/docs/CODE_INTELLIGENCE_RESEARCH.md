# Code Intelligence & Hybrid Design Research

**Date:** 2026-08-20  
**Status:** Evidence-based findings from Research Sprint 2  
**Method:** Deep research with primary sources, repository documentation, arXiv paper, and community benchmarks.

## 1. Executive Summary

`codebase-memory-mcp` is a strong foundational graph engine, but it is not sufficient alone for all our target-stack needs. Our hybrid design must add a wrapper MCP that provides:

- GitNexus-style confidence-ranked impact analysis.
- Call-path + HTTP edge aggregation.
- Optional process tracing if field evidence demands it.
- Flutter/Dart semantic safety through Serena or Dart Analyzer.
- Monorepo-aware indexing with `.cbmignore`.
- Token-capped and summarized outputs.

We do not need to fork `codebase-memory-mcp`. We can build the missing layers ourselves on top of its CLI/MCP outputs.

## 2. Research Questions & Answers

### Q1. Can we extend/wrap codebase-memory-mcp with GitNexus-style process tracing and confidence scores?

**Answer:** Yes. `codebase-memory-mcp` exposes raw graph access via `query_graph` and `get_graph_schema`. It supports CLI mode and stdio. We can post-process `trace_call_path`, `get_architecture`, and `detect_changes` outputs to add confidence scoring and process grouping.

**Confidence:** VERIFIED for raw graph access; INFERRED for wrapper architecture.

**Key implications:**

- Build a wrapper MCP server that calls `codebase-memory-mcp` tools and enriches outputs.
- Use `query_graph` for advanced post-processing.
- Do not fork the repo; stay on MIT safe code.

### Q2. What are the exact Dart/Flutter limitations of codebase-memory-mcp?

**Answer:** Dart is parsed via Tree-sitter, but many Flutter-critical relationships are missing:
- Widget composition tree.
- Provider/Riverpod/Bloc wiring.
- Generated `.g.dart` / `.freezed.dart` files unless pre-generated and indexed.
- Mixins parsed but not semantically resolved.
- Extension types possibly unsupported by grammar.
- Platform channels not traced.
- Part/part-of files not resolved.

**Confidence:** VERIFIED for missing relationships; INFERRED for Serena compensation.

**Key implications:**

- Flutter/Dart safety requires Serena/Dart LSP + `dart analyze` + `flutter analyze` + `flutter test`.
- `codebase-memory-mcp` is advisory only for Flutter impact analysis.
- Document Flutter limitations explicitly in our notChecked list.

### Q3. How does codebase-memory-mcp handle monorepos?

**Answer:** It can index a monorepo root into a single queryable graph using manifest scanning (`package.json`, `pubspec.yaml`, `pyproject.toml`). Cross-package edges are supported via `CROSS_*`. Exclusions are layered: hardcoded → `.gitignore` → `.cbmignore`. Symlinks are always skipped.

**Confidence:** VERIFIED for monorepo support and exclusions; UNVERIFIED for Nx/Turborepo specific behavior.

**Key implications:**

- Index monorepo root once.
- Create `.cbmignore` with `.next/`, `.dart_tool/`, `__pycache__/`, `node_modules/`, `*.pyc`.
- Validate pnpm workspace cross-package resolution early because symlinks are skipped.

### Q4. What is the token cost and output size of graph queries?

**Answer:** Typical outputs:
- `search_graph` simple query: ~200 tokens.
- `trace_call_path` depth 3: ~800 tokens.
- `get_architecture`: ~1,500 tokens.
- `detect_changes`: ~500–3,000 tokens depending on diff.

There is no built-in server-side truncation or pagination. Client-side truncation varies and is unreliable.

**Confidence:** VERIFIED for token sizes and no truncation; INFERRED for summarization strategy.

**Key implications:**

- Our wrapper MCP must cap and summarize outputs before injection.
- Use `LIMIT`, `depth`, risk filters, and `get_graph_schema` first.
- Implement head-tail truncation with a clear marker and optional follow-up retrieval.

### Q5. Do we need a separate process-tracing layer?

**Answer:** No. For FastAPI + Next.js + Flutter, call-path + HTTP edge aggregation is enough. `codebase-memory-mcp` already provides the raw edges and Louvain clusters. Full process clustering is optional later.

**Confidence:** INFERRED.

**Key implications:**

- v1: wrapper MCP aggregates `trace_call_path` + `detect_changes` + `get_architecture`.
- Add confidence heuristics based on resolution signals.
- Defer process clustering until field evidence shows need.

## 3. Consolidated Evidence Table

| # | Claim | Confidence | Source Type |
|---|---|---|---|
| 1 | `query_graph` exposes raw Cypher-like graph access | VERIFIED | Official README / arXiv |
| 2 | `trace_call_path` returns BFS call chains depth 1–5 | VERIFIED | Official README |
| 3 | `get_architecture` includes Louvain clusters | VERIFIED | Official README / arXiv |
| 4 | Confidence scores computed internally but not exposed | VERIFIED | arXiv |
| 5 | CLI mode and stdio mode supported | VERIFIED | Official README |
| 6 | Dart parsed via Tree-sitter, Flutter relationships missing | VERIFIED | Official README / arXiv / Flutter docs |
| 7 | Monorepo single graph via manifest scanning | VERIFIED | Official README |
| 8 | Exclusions via layered `.cbmignore` | VERIFIED | Official README |
| 9 | Symlinks always skipped | VERIFIED | Official README |
| 10 | Token sizes ~200–3,000 tokens depending on tool | VERIFIED | Community benchmarks |
| 11 | No built-in server-side truncation/pagination | VERIFIED | Official README / docs |
| 12 | Client-side truncation varies | VERIFIED | Anthropic / Codex reports |
| 13 | Call-path + HTTP edge aggregation sufficient for our stack | INFERRED | Official README / GitNexus research |
| 14 | Full process clustering not required for v1 | INFERRED | Architecture recommendation |

## 4. Final Hybrid Design Decisions

### Code Intelligence Layer
- Primary graph engine: `codebase-memory-mcp`.
- Wrapper MCP: adds confidence-ranked impact, aggregation, token capping.
- Optional LSP layer: `Serena` for Flutter/Dart and precise symbol edits.
- No fork of `codebase-memory-mcp`.

### Monorepo Strategy
- Index monorepo root once.
- Use `.cbmignore` to exclude generated/build directories.
- Validate pnpm symlink behavior before relying on cross-package edges.

### Flutter Strategy
- Treat `codebase-memory-mcp` as advisory for Dart/Flutter.
- Require Serena/Dart Analyzer and Flutter toolchain for semantic safety.
- Run `dart analyze`, `flutter analyze`, `flutter test` before/after edits.

### Token Management
- Wrapper MCP enforces max output budgets.
- Use `LIMIT`, depth capping, risk filtering, and head-tail truncation.
- Cache stable architecture summaries.

## 5. Open Questions & Risks

- Exact pnpm workspace cross-package resolution must be tested.
- Tree-sitter Dart grammar support for extension types / sealed classes is not confirmed.
- Serena Dart LSP integration details need validation.
- Cross-service HTTP edge accuracy on our actual code is unproven.
- MCP timeout from Cursor may affect wrapper queries; design wrapper tools to return quickly.

## 6. Decisions Influenced

- Hybrid code intelligence architecture confirmed.
- Wrapper MCP is our orchestration and enrichment layer.
- Flutter requires additional LSP/toolchain beyond graph engine.
- Monorepo exclusions and validation added to Phase 0.

## 7. Appendix: Primary Sources

- `codebase-memory-mcp` GitHub: https://github.com/DeusData/codebase-memory-mcp
- arXiv paper: https://arxiv.org/pdf/2603.27277.pdf
- Serena GitHub: https://github.com/oraios/serena
- GitNexus research: https://rywalker.com/research/gitnexus
- Cursor MCP / hooks docs: https://cursor.com/docs/mcp and https://cursor.com/docs/agent/hooks
- Anthropic tool design: https://www.anthropic.com/engineering/writing-tools-for-agents
- Community token benchmark: https://dev.to/deusdata/how-i-cut-my-ai-coding-agents-token-usage-by-120x-with-a-code-knowledge-graph-4a3d

Full detailed findings and source links are preserved in the research session history.
