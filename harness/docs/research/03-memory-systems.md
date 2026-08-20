# Memory Systems Research

**Category:** Persistent memory, context databases, organizational memory, and agent memory layers  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents research on memory systems for AI agents. CTH’s primary context strategy is Git-backed Markdown (`AGENTS.md`, `PROJECT_CONTEXT.md`) with no separate retrieval MCP unless field evidence proves usage. However, we studied these systems to extract useful patterns, especially layered memory and governed knowledge.

## Repositories Evaluated

### 1. TencentDB Agent Memory
- **Repo:** https://github.com/TencentCloud/TencentDB-Agent-Memory
- **License:** MIT
- **Primary language:** TypeScript
- **Maintainers / activity:** Active, ~16.9k stars
- **Status:** Study (borrow patterns only)

#### Key Findings
- Four memory asset types: Chat Memory (L0–L3), Skill, Wiki, CodeGraph.
- L0–L3 semantic pyramid:
  - L0: raw conversations
  - L1: atomic facts
  - L2: scenario blocks
  - L3: persona/core
- Layered retrieval: bootstrap from L2/L3, drill down into L1/L0.
- Agent loadouts bind specific memory assets to specific agents.
- Visibility: private/team/restricted/agent.
- Governance: human review and versioning.

#### CTH Usage
- Borrow layered context idea for our Git-backed context bundle.
- Use L2/L3 equivalent in `AGENTS.md`; L1/L2 in `PROJECT_CONTEXT.md`; L0 audit in logs.
- Do not run as a service.

#### Evidence
- Research session data.

---

### 2. Mem0
- **Repo:** https://github.com/mem0ai/mem0
- **License:** Verify (Apache-2.0 / commercial features)
- **Primary language:** Python
- **Maintainers / activity:** Active
- **Status:** Optional (already in Constellation as local SQLite)

#### Key Findings
- Drop-in persistent memory layer across agent frameworks.
- Local and cloud options.
- Not a contract in Constellation; no field evidence of usage.

#### CTH Usage
- Keep optional local Mem0 for now; do not rely on it.

#### Evidence
- Constellation docs, research sessions.

---

### 3. GBrain
- **Repo:** https://github.com/garrytan/gbrain
- **License:** MIT
- **Primary language:** TypeScript/Python
- **Maintainers / activity:** Active
- **Status:** Study (borrow governance pattern)

#### Key Findings
- Git-backed Markdown system of record.
- Hybrid search (BM25 + vector) and typed knowledge graph.
- Synthesis, citations, gap analysis, contradiction detection.
- MCP access with multi-user scopes.
- Separate workspace vs brain repos.

#### CTH Usage
- Borrow Git-backed knowledge + human review pattern for our context bundle.
- Not needed as a full company brain.

#### Evidence
- Research session data.

---

### 4. Cognee
- **Repo:** https://github.com/topoteretes/cognee
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Exclude for v1

#### Key Findings
- Graph + vector memory from documents, code, conversations.
- Persistent long-term memory for agents.
- Multiple backends (SQLite, Postgres, Neo4j, etc.).

#### CTH Usage
- Not needed now; may consider later if cross-project memory becomes a field need.

#### Evidence
- Research session data.

---

### 5. Graphiti
- **Repo:** https://github.com/getzep/graphiti
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- Temporal knowledge graph; tracks changing facts and relationships over time.

#### CTH Usage
- Not needed for single-project harness.

#### Evidence
- Research session data.

---

### 6. Hindsight
- **Repo:** https://github.com/vectorize-io/hindsight
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- Reflective memory: retain, recall, reflect; facts, experiences, mental models.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 7. MemOS
- **Repo:** https://github.com/MemTensor/MemOS
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- Memory operating system for agents; layered, persistent, skill memory.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 8. Honcho
- **Repo:** https://github.com/plastic-labs/honcho
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- User/agent modeling, cross-session context, evolving relationships.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 9. Zep
- **Repo:** https://github.com/getzep/zep
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- Temporal conversational memory for agents.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 10. Supermemory
- **Repo:** https://github.com/supermemoryai/supermemory
- **License:** Verify
- **Status:** Exclude for v1

#### Key Findings
- Cross-agent persistent memory.

#### CTH Usage
- Not needed now.

#### Evidence
- Research session data.

---

### 11. Letta
- **Repo:** https://github.com/letta-ai/letta
- **License:** Verify
- **Status:** Study

#### Key Findings
- Memory-native stateful agents; persistent state as core primitive.

#### CTH Usage
- Borrow memory-native state ideas, but CTH uses Git-backed files.

#### Evidence
- Research session data.

---

### 12. OpenViking
- **Repo:** https://github.com/volcengine/OpenViking
- **License:** AGPL-3.0 (main project)
- **Status:** Exclude (AGPL risk)

#### Key Findings
- Context database with hierarchical memory/resource/skill filesystem (`viking://`).
- L0/L1/L2 detail levels.
- AGPL main license complicates commercial embedding.

#### CTH Usage
- Borrow hierarchical context navigation concept; do not adopt.

#### Evidence
- Research session data.

---

## Patterns We Reuse from Memory Systems

| Pattern | Source | CTH Implementation |
|---------|--------|-------------------|
| Layered context pyramid | TencentDB Agent Memory | Summary → decisions → artifacts |
| Git-backed knowledge with review | GBrain / TeamBrain | `.team/` state + context bundle in Git |
| Hierarchical context navigation | OpenViking | `AGENTS.md` summary + detailed artifacts |
| Governance / approvals for memory writes | GBrain / TeamBrain | Human approval for phase transitions |
| Avoid retrieval MCP unless used | Constellation field evidence | Preloaded context bundle, no memory MCP in v1 |

## Decision Summary
- No separate memory MCP in v1.
- Git-backed Markdown remains the primary context and memory mechanism.
- Revisit memory systems only if field evidence shows agents use retrieval and need cross-project recall.

## Sources
- Research session data.
- Constellation docs and earlier source inspections.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial memory systems category research |
