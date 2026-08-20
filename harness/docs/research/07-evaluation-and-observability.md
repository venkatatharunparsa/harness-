# Evaluation and Observability Research

**Category:** Evaluation, tracing, logging, context optimization, and agent activity tracking  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents evaluation and observability tools studied for CTH. Our harness must be measurable, auditable, and research-ready. We adopt lightweight logging from day one and may add richer tracing/evaluation later if field evidence supports it.

## Repositories Evaluated

### 1. Promptfoo
- **Repo:** https://github.com/promptfoo/promptfoo
- **License:** MIT
- **Primary language:** TypeScript
- **Maintainers / activity:** Active
- **Status:** Adopt

#### Key Findings
- Agent regression and red-team testing.
- Supports custom assertions and evaluation matrices.
- Can test prompt/tool behavior against expected outputs.

#### CTH Usage
- Use for regression testing of harness rules and gate behavior.
- Evaluate gate call compliance, tool usage, and role transitions.

#### Evidence
- Research session data.

---

### 2. Langfuse
- **Repo:** https://github.com/langfuse/langfuse
- **License:** MIT
- **Primary language:** TypeScript/Python
- **Maintainers / activity:** Active
- **Status:** Adopt (optional)

#### Key Findings
- Open-source LLM observability: traces, prompts, datasets, scores.
- Can track tool calls, token usage, and session flows.

#### CTH Usage
- Optional observability backend for harness metrics.
- We can start with local JSONL logs and integrate Langfuse later if needed.

#### Evidence
- Research session data.

---

### 3. Phoenix
- **Repo:** https://github.com/Arize-ai/phoenix
- **License:** Apache-2.0
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Tracing and evaluation for LLM apps.
- Local or hosted.

#### CTH Usage
- Alternative to Langfuse for observability.

#### Evidence
- Research session data.

---

### 4. tiktoken
- **Repo:** https://github.com/openai/tiktoken
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Fast BPE tokenizer for OpenAI models.
- Useful for token counting and context budgeting.

#### CTH Usage
- Use in wrapper MCP to measure output sizes and enforce token limits before injecting into agent context.

#### Evidence
- Research session data.

---

### 5. outlines
- **Repo:** https://github.com/dottxt-ai/outlines
- **License:** Apache-2.0
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Structured output generation: guaranteed valid JSON, regex, Pydantic models.
- Could help enforce schema-valid artifact generation.

#### CTH Usage
- Optional for producing schema-valid artifacts, but our gates already validate files on disk. We may not need this.

#### Evidence
- Research session data.

---

### 6. codeburn
- **Repo:** https://github.com/getagentseal/codeburn
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Local AI coding activity tracking with dashboard.
- Tracks sessions, file changes, summaries.

#### CTH Usage
- Our `.team/audit.jsonl` and logs already capture similar data. Not needed.

#### Evidence
- Research session data.

---

### 7. llmlingua
- **Repo:** https://github.com/microsoft/LLMLingua
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Prompt compression for long context.
- Could reduce token usage in context bundles.

#### CTH Usage
- Not needed now; our context bundle is already concise and Git-backed.

#### Evidence
- Research session data.

---

### 8. gptcache
- **Repo:** https://github.com/zilliztech/GPTCache
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional

#### Key Findings
- Semantic cache for LLM responses.

#### CTH Usage
- Not needed for v1.

#### Evidence
- Research session data.

---

### 9. headroom
- **Repo:** Verify URL
- **License:** Verify
- **Primary language:** Unknown
- **Status:** Optional

#### Key Findings
- Context compression to reduce token usage.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 10. DeepEval
- **Repo:** https://github.com/confident-ai/deepeval
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Optional

#### Key Findings
- LLM/agent evaluation framework.

#### CTH Usage
- Alternative to Promptfoo; we can choose one later.

#### Evidence
- Research session data.

---

### 11. Ragas
- **Repo:** https://github.com/explodinggradients/ragas
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Exclude

#### Key Findings
- RAG evaluation framework.

#### CTH Usage
- Not needed; we are not building RAG.

#### Evidence
- Research session data.

---

### 12. MemoryData
- **Repo:** Verify URL
- **License:** Verify
- **Status:** Optional (future)

#### Key Findings
- Unified memory benchmarks for agents.

#### CTH Usage
- Could be useful for evaluating memory/context later.

#### Evidence
- Research session data.

---

### 13. LiteLLM
- **Repo:** https://github.com/BerriAI/litellm
- **License:** MIT
- **Primary language:** Python
- **Status:** Optional

#### Key Findings
- Unified model gateway with routing, fallback, budgets, and OpenAI-compatible API.

#### CTH Usage
- Not needed now; Cursor uses its own model config.

#### Evidence
- Research session data.

---

### 14. vLLM / Ollama
- **Repo:** https://github.com/vllm-project/vllm / https://github.com/ollama/ollama
- **License:** Apache-2.0 / MIT
- **Status:** Optional

#### Key Findings
- Local model serving and inference.
- Not required for CTH unless we want local models.

#### CTH Usage
- Not needed for v1.

#### Evidence
- Research session data.

---

### 15. Pipecat / LiveKit Agents / TEN Framework
- **Repo:** Various
- **License:** BSD-2 / Apache-2.0 / Apache-2.0
- **Status:** Exclude

#### Key Findings
- Voice and multimodal agent pipelines.

#### CTH Usage
- Not needed for coding harness.

#### Evidence
- Research session data.

---

### 16. Browser Use / Stagehand / Playwright
- **Repo:** Various
- **License:** MIT / MIT / Apache-2.0
- **Status:** Exclude for v1

#### Key Findings
- Browser automation and computer-use agents.

#### CTH Usage
- Not needed for v1 coding harness.

#### Evidence
- Research session data.

---

## Measurement Strategy for CTH

### Core Metrics
- Gate call rate without explicit reminder.
- Phase approval/rejection ratio.
- Time per phase.
- Number of loops back to fix issues.
- Indexing time and query latency.
- `detect_changes` precision/recall.
- Context staleness rate.
- Agent edit correctness rate.
- Token consumption per task.

### Logging Approach
- Append-only JSONL audit log via `pino` or `structlog`.
- Structured fields: timestamp, event type, actor, phase, commit, tool, result.
- Separate research diary for qualitative findings.

### Evaluation Approach
- Baseline without harness vs. with harness.
- Controlled test changes for code intelligence precision/recall.
- Regression tests with Promptfoo once stable.

## Decision Summary
- Adopt Promptfoo for evaluation.
- Optional Langfuse/Phoenix for observability.
- Use tiktoken for token budgeting.
- Keep logging lightweight initially.
- Exclude voice/browser/model-serving tools.

## Sources
- Research session data.
- Earlier repository atlas data.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial evaluation and observability category research |
