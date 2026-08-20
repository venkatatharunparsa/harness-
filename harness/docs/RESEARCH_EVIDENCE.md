# Research Evidence: Constellation Team Harness (CTH)

**Version:** 0.1  
**Status:** Living document  
**Owner:** Human lead  
**Date:** 2026-08-20

## Purpose
This document consolidates all repository and tool research conducted for CTH. It serves as the master evidence index, linking each repository to its category, license, status, relevance, and key findings. It does not replace the detailed research docs; it provides a structured audit trail and proof of due diligence.

## Methodology
- Primary-source inspection where possible (source code, LICENSE, README, issues).
- Perplexity deep research for external analysis.
- Cursor source-inspection for `codebase-memory-mcp` and `Serena`.
- Classification: High / Medium / Low relevance.
- Status: Adopt / Study / Exclude / Pilot.
- All claims are labeled with confidence where applicable.

---

## Master Repository Index

| Repository | Category | License | Status | Relevance | Key Finding | Source / Date |
|-----------|----------|---------|--------|-----------|-------------|---------------|
| OpenHands | Agent platform | MIT | Study | High | Event-sourced state, risk-based confirmation, secret registry, condensation | Cursor source report 2026-08-20 |
| codebase-memory-mcp | Code graph | MIT | Adopt | High | Persistent SQLite graph, trace_path, detect_changes, embedded hybrid LSP, Flutter weak | Source inspection 2026-08-20 |
| Serena | LSP toolkit | MIT | Adopt | High | LSP-grade symbol ops, Dart LS, project memory, no graph | Source inspection 2026-08-20 |
| GitNexus | Code graph | PolyForm NC | Exclude | Medium | Strong process tracing/confidence, license blocks commercial use | Research 2026-08-20 |
| CodeGraph | Code graph | Apache-2.0 | Pilot | Medium | Integrated edit context, PR impact, solo maintainer risk | Research 2026-08-20 |
| DeepSeek Harness | Agent harness | MIT | Study | Medium | Plugin architecture, append-only log, resume/fork | Research 2026-08-20 |
| TencentDB Agent Memory | Memory hub | MIT | Study | Medium | L0-L3 memory pyramid, agent loadout; borrow pattern only | Research 2026-08-20 |
| Agent-Reach | Web capability | MIT | Optional | Low | Zero-cost multi-platform web access; only for research phase | Research 2026-08-20 |
| dependency-cruiser | Architecture | MIT | Adopt | High | Deterministic circular dependency/boundary checks | Hybrid stack 2026-08-20 |
| eslint-plugin-boundaries | Architecture | MIT | Adopt | Medium | Import boundary enforcement for TS/Next.js | Hybrid stack 2026-08-20 |
| Semgrep | Security | LGPL-2.1 | Adopt | High | Extensible static analysis rules for custom checks | Gates research 2026-08-20 |
| Temporal | Workflow | MIT | Study | Medium | Durable workflow patterns; borrow concepts not dependency | Research 2026-08-20 |
| OpenShell | Sandbox | Verify | Study | Medium | Policy-enforced execution; borrow enforcement ideas | Research 2026-08-20 |
| Pipelock | Firewall | Verify | Study | Medium | MCP egress/DLP; borrow boundary ideas | Research 2026-08-20 |
| Agentgateway | Protocol gateway | Apache-2.0 | Study | Medium | Centralized MCP/A2A auth/routing/telemetry | Research 2026-08-20 |
| repowise | Code intelligence | AGPL-3.0 | Exclude | Medium | Five intelligence layers, code health; AGPL risk | Research 2026-08-20 |
| code-review-graph | Code graph | MIT | Pilot | Medium | Blast-radius focused, token efficient | Research 2026-08-20 |
| Graphify | Code graph | Verify | Pilot | Low | Visual repo graph; not for editing | Research 2026-08-20 |
| tiktoken | Tokenizer | MIT | Optional | Low | Token counting for context budgets | Research 2026-08-20 |
| outlines | Structured output | Apache-2.0 | Optional | Low | Guaranteed JSON/regex output generation | Research 2026-08-20 |
| codeburn | Activity tracking | MIT | Optional | Low | AI coding session tracking; not core | Research 2026-08-20 |
| llmlingua | Prompt compression | MIT | Optional | Low | Long-context compression; not needed now | Research 2026-08-20 |
| gptcache | Semantic cache | MIT | Optional | Low | LLM response caching; not needed now | Research 2026-08-20 |
| headroom | Context compression | Verify | Optional | Low | Token reduction; possibly useful later | Research 2026-08-20 |
| DeerFlow | Agent harness | Verify | Study | Low | Sub-agent orchestration, skills; not IDE-native | Research 2026-08-20 |
| Hermes Agent | Agent | MIT | Study | Low | Self-improving skills; not IDE-native | Research 2026-08-20 |
| OpenClaw | Gateway | Verify | Study | Low | Multi-channel agent gateway; not needed | Research 2026-08-20 |
| Goose | Developer harness | Verify | Study | Low | MCP extension patterns; not needed | Research 2026-08-20 |
| GBrain | Company brain | MIT | Study | Low | Git-backed shared memory; borrow pattern | Research 2026-08-20 |
| Onyx | Enterprise search | Verify | Exclude | Low | Overkill for single-project harness | Research 2026-08-20 |
| OpenViking | Context DB | AGPL-3.0 | Exclude | Low | AGPL risk; hierarchical context pattern only | Research 2026-08-20 |
| Cognee | Memory graph | Apache-2.0 | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Graphiti | Temporal graph | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Hindsight | Reflective memory | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| MemOS | Memory OS | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Honcho | User modeling | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Mem0 | Memory layer | Verify | Optional | Low | Already optional in Constellation; no evidence of use | Research 2026-08-20 |
| Zep | Memory | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Supermemory | Memory | Verify | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Letta | Agent memory | Verify | Study | Low | Memory-native agent patterns | Research 2026-08-20 |
| LangGraph | Agent framework | MIT | Exclude | Low | We are not building a new agent framework | Research 2026-08-20 |
| CrewAI | Agent framework | Verify | Exclude | Low | Not needed | Research 2026-08-20 |
| AutoGen | Agent framework | CC-BY/MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Mastra | Agent framework | MIT | Exclude | Low | TypeScript framework, but not needed | Research 2026-08-20 |
| PydanticAI | Agent framework | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| LlamaIndex | RAG framework | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Haystack | RAG framework | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| DSPy | Prompt optimization | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Semantic Kernel | Agent framework | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Google ADK | Agent framework | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Smolagents | Agent framework | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| CAMEL | Agent research | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| OpenAI Agents SDK | Agent framework | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| mcp-agent | MCP framework | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| MCP | Protocol | MIT | Adopt | High | Core protocol for all tools | Research 2026-08-20 |
| A2A | Protocol | Apache-2.0 | Defer | Low | Multi-agent later | Research 2026-08-20 |
| ACP | Protocol | Verify | Defer | Low | Multi-agent later | Research 2026-08-20 |
| Restate | Workflow | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Hatchet | Workflow | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Inngest | Workflow | Verify | Exclude | Low | Not needed | Research 2026-08-20 |
| Prefect | Workflow | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Kestra | Orchestration | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| n8n | Automation | Fair-code | Exclude | Low | Not needed | Research 2026-08-20 |
| Dagster | Data workflow | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Airflow | Workflow | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Camunda | BPMN | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Celery | Queue | BSD | Exclude | Low | Not needed for harness; may be part of target apps | Research 2026-08-20 |
| Dagu | Workflow | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Windmill | Workflow | AGPL-3.0 | Exclude | Low | AGPL risk | Research 2026-08-20 |
| OpenSandbox | Sandbox | Verify | Study | Low | General sandbox | Research 2026-08-20 |
| Kubernetes Agent Sandbox | Sandbox | Apache-2.0 | Defer | Low | Later if needed | Research 2026-08-20 |
| gVisor | Sandbox | Apache-2.0 | Defer | Low | Later | Research 2026-08-20 |
| Firecracker | MicroVM | Apache-2.0 | Defer | Low | Later | Research 2026-08-20 |
| Microsandbox | Sandbox | Verify | Defer | Low | Later | Research 2026-08-20 |
| nsjail | Sandbox | Apache-2.0 | Defer | Low | Later | Research 2026-08-20 |
| bubblewrap | Sandbox | LGPL | Defer | Low | Later | Research 2026-08-20 |
| LlamaFirewall | Security | Verify | Study | Medium | Agent security; borrow ideas | Research 2026-08-20 |
| NeMo Guardrails | Guardrails | Apache-2.0 | Study | Low | Conversational/tool rails; possibly later | Research 2026-08-20 |
| Guardrails AI | Validation | Apache-2.0 | Optional | Low | Structured output validation | Research 2026-08-20 |
| Presidio | PII | MIT | Optional | Low | PII detection for logs | Research 2026-08-20 |
| SkillSpector | Skill security | Apache-2.0 | Optional | Medium | Skill supply-chain scanning | Research 2026-08-20 |
| Garak | LLM vuln scan | Apache-2.0 | Optional | Low | Later | Research 2026-08-20 |
| PyRIT | Red team | MIT | Optional | Low | Later | Research 2026-08-20 |
| Promptfoo | Eval | MIT | Adopt | Medium | Agent regression/red-team tests | Research 2026-08-20 |
| DeepEval | Eval | Apache-2.0 | Optional | Low | Later | Research 2026-08-20 |
| Phoenix | Tracing | Apache-2.0 | Optional | Low | Later | Research 2026-08-20 |
| Langfuse | Observability | MIT | Adopt | Medium | Traces/evals for harness metrics | Research 2026-08-20 |
| Ragas | RAG eval | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| LiteLLM | Model gateway | MIT | Optional | Low | For multi-model routing later | Research 2026-08-20 |
| vLLM | Serving | Apache-2.0 | Exclude | Low | Not needed now | Research 2026-08-20 |
| Ollama | Local models | MIT | Optional | Low | If local inference desired | Research 2026-08-20 |
| Pipecat | Voice | BSD-2 | Exclude | Low | Not needed | Research 2026-08-20 |
| LiveKit Agents | Voice/realtime | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| TEN Framework | Multimodal | Apache-2.0 | Exclude | Low | Not needed | Research 2026-08-20 |
| Browser Use | Browser | MIT | Exclude | Low | Not needed for v1 | Research 2026-08-20 |
| Stagehand | Browser | MIT | Exclude | Low | Not needed | Research 2026-08-20 |
| Playwright | Browser | Apache-2.0 | Exclude | Low | Possibly for web testing later | Research 2026-08-20 |
| Sourcegraph | Code search | Verify | Exclude | Low | Enterprise platform, overkill | Research 2026-08-20 |
| Aider repo map | Code context | Apache-2.0 | Study | Low | Ranked repo map concept | Research 2026-08-20 |
| CocoIndex | Indexing | Verify | Exclude | Low | Tutorial, not mature | Research 2026-08-20 |
| CodeAlive MCP | Code search | Verify | Exclude | Low | Hosted/API, not local | Research 2026-08-20 |

---

## Detailed Repository Evidence Entries

### 1. OpenHands
- **Repo:** https://github.com/All-Hands-AI/OpenHands
- **License:** MIT
- **Status:** Study
- **Relevance:** High – production-grade agent architecture patterns.
- **Key findings:**
  - Event-sourced state management with append-only EventLog.
  - SecurityAnalyzer + ConfirmationPolicy for risk-based user approval.
  - SecretRegistry for secret lifecycle management.
  - Context condensation to manage LLM context.
  - Workspace abstraction for opt-in sandboxing.
  - MCP as first-class SDK tools.
- **CTH usage:** Borrow patterns for state, approval, secrets, and context; do not adopt as dependency.

### 2. codebase-memory-mcp
- **Repo:** https://github.com/DeusData/codebase-memory-mcp
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – primary persistent structural graph.
- **Key findings (source-verified):**
  - SQLite graph with nodes/edges; not a library; MCP/CLI only.
  - Incremental indexing via SHA-256 content hash; renaming can trigger FULL rebuild.
  - Watcher is git-poll based, not fsnotify.
  - `trace_path` provides BFS call paths with optional risk labels; `detect_changes` has no confidence/risk.
  - Dart is grammar-level only; no Flutter semantic resolution.
  - pnpm symlinks skipped.
  - Windows idle CPU / stale rendezvous issues.
- **CTH usage:** Use as MCP binary; wrap with our `team-workflow` MCP for confidence, process aggregation, token capping, and stale detection.

### 3. Serena
- **Repo:** https://github.com/oraios/serena
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – precision LSP-grade semantic editing.
- **Key findings (source-verified):**
  - Python MCP toolkit using SolidLSP to spawn language servers.
  - Python default LS is Pyright; TS/JS via typescript-language-server; Dart via Dart Analysis Server.
  - Tools return JSON strings/status; no confidence fields; no structured error envelope.
  - No built-in pagination; max 150k chars output, 240s timeout.
  - Language server lifecycle fail-fast on init; retry once on crash.
  - No Flutter-specific backend; Dart only.
  - Monorepo: multiple `language_servers` in `.serena/project.yml`.
- **CTH usage:** Use as MCP binary for exact symbol operations and edits; wrap with our MCP for output control and error mapping.

### 4. GitNexus
- **Repo:** https://github.com/abhigyanpatwari/GitNexus
- **License:** PolyForm Noncommercial 1.0.0
- **Status:** Exclude (commercial blocker)
- **Relevance:** Medium – concepts worth borrowing.
- **Key findings:**
  - Strong process tracing, confidence-ranked impact, cross-repo groups.
  - License prevents commercial use without separate license.
- **CTH usage:** Rebuild similar workflows on top of `codebase-memory-mcp`; do not use code.

### 5. CodeGraph
- **Repo:** https://github.com/codegraph-ai/CodeGraph
- **License:** Apache-2.0
- **Status:** Pilot
- **Relevance:** Medium – integrated alternative.
- **Key findings:**
  - Persistent RocksDB graph, incremental indexing, `get_edit_context`, `pr_context`.
  - Solo maintainer, smaller community.
  - Dart may require extra build.
- **CTH usage:** Pilot candidate if `codebase-memory-mcp` gaps appear.

### 6. DeepSeek Harness
- **Repo:** https://github.com/deepseek-ai/deepseek-harness
- **License:** MIT
- **Status:** Study
- **Relevance:** Medium – plugin architecture, session log.
- **CTH usage:** Borrow plugin-first modularity and append-only session replay patterns.

### 7. TencentDB Agent Memory
- **Repo:** https://github.com/TencentCloud/TencentDB-Agent-Memory
- **License:** MIT
- **Status:** Study
- **Relevance:** Medium – layered memory concept.
- **CTH usage:** Borrow L0-L3 context pyramid for our Git-backed context bundle; do not run service.

### 8. Agent-Reach
- **Repo:** https://github.com/Panniantong/agent-reach
- **License:** MIT
- **Status:** Optional
- **Relevance:** Low – web research for Product Analyst phase.
- **CTH usage:** Optional MCP/CLI for research tasks; evaluate usage before adopting.

### 9. dependency-cruiser
- **Repo:** https://github.com/sverweij/dependency-cruiser
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – architecture gate enforcement.
- **CTH usage:** Integrate into System Gate v2 fitness functions for circular dependency and boundary checks.

### 10. Semgrep
- **Repo:** https://github.com/semgrep/semgrep
- **License:** LGPL-2.1
- **Status:** Adopt
- **Relevance:** High – security gate extension.
- **CTH usage:** Add to Sentinel v2 for custom static analysis rules; run as external CLI.

### 11. Temporal
- **Repo:** https://github.com/temporalio/temporal
- **License:** MIT
- **Status:** Study
- **Relevance:** Medium – durable workflow patterns.
- **CTH usage:** Borrow deterministic state/approval/retry patterns; do not adopt as service.

### 12. Promptfoo
- **Repo:** https://github.com/promptfoo/promptfoo
- **License:** MIT
- **Status:** Adopt
- **Relevance:** Medium – agent evaluation.
- **CTH usage:** Use for regression/red-team tests of harness behavior.

### 13. Langfuse
- **Repo:** https://github.com/langfuse/langfuse
- **License:** MIT
- **Status:** Adopt
- **Relevance:** Medium – tracing/metrics.
- **CTH usage:** Optional for observability; can use lightweight local logs first.

### 14. pino / structlog
- **Repo:** https://github.com/pinojs/pino / https://github.com/hynek/structlog
- **License:** MIT / MIT or Apache-2.0
- **Status:** Adopt
- **Relevance:** High – JSONL audit logging.
- **CTH usage:** Use in team-workflow MCP implementation.

### 15. gray-matter
- **Repo:** https://github.com/jonschlinkert/gray-matter
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – context bundle parsing.
- **CTH usage:** Parse YAML frontmatter in AGENTS.md / PROJECT_CONTEXT.md.

### 16. chokidar
- **Repo:** https://github.com/paulmillr/chokidar
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – file watcher.
- **CTH usage:** Watch `.team/state.json` and context files.

### 17. gitleaks
- **Repo:** https://github.com/gitleaks/gitleaks
- **License:** MIT
- **Status:** Adopt
- **Relevance:** High – secrets detection.
- **CTH usage:** Keep in Sentinel core.

### 18. trivy
- **Repo:** https://github.com/aquasecurity/trivy
- **License:** Apache-2.0
- **Status:** Adopt
- **Relevance:** High – dependency/IaC/container scanning.
- **CTH usage:** Keep in Sentinel core.

### 19. opengrep
- **Repo:** https://github.com/opengrep/opengrep
- **License:** AGPL-3.0 (verify)
- **Status:** Adopt with license review
- **Relevance:** High – SAST engine.
- **CTH usage:** Existing Sentinel core; verify license compatibility for commercial SaaS.

### 20. osv-scanner
- **Repo:** https://github.com/google/osv-scanner
- **License:** Apache-2.0
- **Status:** Adopt
- **Relevance:** High – dependency vulnerability scanning.
- **CTH usage:** Add to Sentinel supply-chain stage.

### 21. slsa-verifier / cosign
- **Repo:** https://github.com/slsa-framework/slsa-verifier / https://github.com/sigstore/cosign
- **License:** Apache-2.0
- **Status:** Adopt
- **Relevance:** Medium – supply chain provenance.
- **CTH usage:** Add to Sentinel supply-chain stage.

### 22. OWASP ZAP / nuclei
- **Repo:** https://github.com/zaproxy/zaproxy / https://github.com/projectdiscovery/nuclei
- **License:** Apache-2.0 / MIT
- **Status:** Optional
- **Relevance:** Medium – DAST.
- **CTH usage:** Add to Sentinel dynamic stage for pre-release.

### 23. falco / tracee
- **Repo:** https://github.com/falcosecurity/falco / https://github.com/aquasecurity/tracee
- **License:** Apache-2.0
- **Status:** Optional
- **Relevance:** Medium – runtime security.
- **CTH usage:** Add to Sentinel runtime stage.

### 24. mobSF / qark
- **Repo:** https://github.com/MobSF/Mobile-Security-Framework-MobSF / https://github.com/linkedin/qark
- **License:** GPL-3.0 / Apache-2.0 (verify)
- **Status:** Optional
- **Relevance:** Medium – mobile security.
- **CTH usage:** Add to Sentinel platform stage for mobile apps.

### 25. checkov / terrascan
- **Repo:** https://github.com/bridgecrewio/checkov / https://github.com/tenable/terrascan
- **License:** Apache-2.0 / Apache-2.0
- **Status:** Adopt
- **Relevance:** High – IaC policy enforcement.
- **CTH usage:** Add to Sentinel platform stage.

### 26. gopass / vault
- **Repo:** https://github.com/gopasspw/gopass / https://github.com/hashicorp/vault
- **License:** MIT / MPL-2.0
- **Status:** Optional
- **Relevance:** Medium – secrets management.
- **CTH usage:** Add to secrets audit stage.

### 27. smolagents / CAMEL / etc.
- **Status:** Exclude
- **Relevance:** Low
- **Reason:** Agent frameworks not needed; we are building IDE-native harness, not new agent runtime.

---

## Source Inspection Reports
- `context/CODEBASE_MEMORY_MCP_SOURCE_NOTES.md`
- `context/SERENA_SOURCE_NOTES.md`

## Research Sprint Summaries
- `docs/CURSOR_ENFORCEMENT_RESEARCH.md`
- `docs/CODE_INTELLIGENCE_RESEARCH.md`
- `docs/GATES_RESEARCH.md`

## Open Questions / Remaining Validation
- Confirm opengrep license.
- Validate Serena Dart LS on actual Flutter project.
- Validate pnpm monorepo cross-package edges.
- Confirm Cursor MCP tool discoverability on current version.
- Test codebase-memory-mcp and Serena in Cursor sandbox.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial master index and first batch of detailed entries |
