# Agent Harness Research

**Category:** Complete agent platforms, runtimes, and harness reference architectures  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents research on complete agent harnesses and autonomous coding platforms. We do not adopt these as dependencies, but we extract architectural patterns that directly influence CTH’s design:

- Event-sourced state and recovery
- Risk-based confirmation and approval flows
- Plugin/modular architecture
- Context management and condensation
- Sub-agent delegation and orchestration
- Secret management

## Repositories Evaluated

### 1. OpenHands
- **Repo:** https://github.com/All-Hands-AI/OpenHands
- **License:** MIT
- **Primary language:** Python
- **Maintainers / activity:** All Hands AI, very active, 76k+ stars
- **Status:** Study (reference architecture)

#### Key Findings
- Event-sourced state management using `ConversationState` + append-only `EventLog`.
- Crash recovery under 20ms; deterministic replay and pause/resume.
- SecurityAnalyzer + ConfirmationPolicy for risk-based human approval.
- SecretRegistry with masking, encryption, and live rotation.
- CodeAct paradigm: all actions expressed as executable code.
- Context condensation: summarization of old events while preserving full log.
- Workspace abstraction: local-first by default, sandboxing opt-in.
- MCP integration as first-class SDK tools.
- Multi-agent delegation via child conversations.

#### CTH Usage
- Borrow event-sourced state for our `.team/state.json` + `.team/audit.jsonl`.
- Borrow risk-based confirmation for our `preToolUse`/shell hooks.
- Borrow secret management ideas for Sentinel v2.
- Borrow context condensation for our Git-backed context bundle.
- Do not adopt as dependency.

#### Evidence
- Cursor source report 2026-08-20.
- Official repository and docs.

---

### 2. DeepSeek Harness
- **Repo:** https://github.com/deepseek-ai/deepseek-harness
- **License:** MIT
- **Primary language:** TypeScript/Node
- **Maintainers / activity:** DeepSeek AI, developer preview
- **Status:** Study

#### Key Findings
- Plugin-first architecture: everything is a plugin, including models, tools, skills, sessions, sandboxes.
- Built on Cordis plugin meta-framework.
- Append-only session log with trajectory view for replay, fork, search, resume.
- Runtime modes: Standard, Code, Minimal, Creator.
- Model-agnostic plugin architecture.
- Developer preview: breaking changes expected.

#### CTH Usage
- Borrow plugin-first modularity to keep CTH components swappable.
- Borrow append-only session log and trajectory/resume patterns.
- Do not adopt as runtime; CTH is Cursor-native.

#### Evidence
- Research session data.

---

### 3. DeerFlow
- **Repo:** https://github.com/bytedance/deer-flow
- **License:** Verify
- **Primary language:** Python
- **Status:** Study (low relevance for CTH core)

#### Key Findings
- Long-horizon super-agent harness with sub-agents, skills, memory, sandbox, context engineering, scheduled tasks, MCP.
- Strong artifact and observability support.
- Skill restrictions are behavioral, not hard security boundaries.

#### CTH Usage
- Borrow sub-agent skill/artifact patterns, but CTH uses IDE-native roles.
- Not directly integrated.

#### Evidence
- Research session data.

---

### 4. Hermes Agent
- **Repo:** https://github.com/NousResearch/hermes-agent
- **License:** MIT
- **Primary language:** Python
- **Status:** Study (low relevance)

#### Key Findings
- Self-improving personal assistant with skills, memory, scheduling, sub-agents, multiple backends.
- Self-modification requires external sandboxing.

#### CTH Usage
- Borrow idea of skills creation from successful workflows, but our skills are static Markdown roles/context.

#### Evidence
- Research session data.

---

### 5. OpenClaw
- **Repo:** https://github.com/openclaw/openclaw
- **License:** Verify
- **Primary language:** TypeScript/Node
- **Status:** Study (low relevance)

#### Key Findings
- Personal-agent gateway with multi-channel messaging, device nodes, voice, sessions, skills.
- Main session vs sandboxed sessions; broad assistant surface.

#### CTH Usage
- Not needed for IDE coding harness.

#### Evidence
- Research session data.

---

### 6. Goose
- **Repo:** https://github.com/block/goose
- **License:** Verify
- **Primary language:** Rust/TypeScript
- **Status:** Study (low relevance)

#### Key Findings
- Extensible local developer harness with MCP extension points and recipes.
- Provider-neutral, terminal-driven.

#### CTH Usage
- Borrow MCP extension patterns, but CTH targets Cursor specifically.

#### Evidence
- Research session data.

---

## Patterns We Are Reusing Across CTH

| Pattern | Source | CTH Implementation |
|---------|--------|-------------------|
| Event-sourced state | OpenHands | Append-only `.team/audit.jsonl` + snapshot `.team/state.json` |
| Risk-based approval | OpenHands | `preToolUse` + shell hook risk checks + human approval |
| Secret lifecycle | OpenHands | Sentinel v2 secrets stage |
| Context condensation | OpenHands, DeepSeek Harness | Context bundle summarization |
| Plugin modularity | DeepSeek Harness | Swappable MCP components and host adapters |
| Append-only session log | DeepSeek Harness, OpenHands | Structured JSONL logs |
| Sub-agent/roles | DeerFlow, Hermes, OpenHands | Team roles/phases in `team-workflow` MCP |

## Decision Summary
- None of these are adopted as runtime dependencies.
- We extract and reimplement only the proven concepts that fit an IDE-native harness.

## Sources
- Research session data.
- Official repositories and documentation.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial agent harness category research |
