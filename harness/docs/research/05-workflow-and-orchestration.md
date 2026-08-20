# Workflow and Orchestration Research

**Category:** Durable workflows, orchestration engines, state machines, and architecture boundary enforcement  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents research on workflow engines, durable execution frameworks, and architecture boundary tools evaluated for CTH. While CTH itself is IDE-native and does not currently run a separate workflow engine, we borrow deterministic state, retry, approval, and boundary enforcement concepts.

We also include architecture boundary enforcement tools because they are deterministic and form part of our System Gate v2 fitness functions.

## Repositories Evaluated

### 1. Temporal
- **Repo:** https://github.com/temporalio/temporal
- **License:** MIT
- **Primary language:** Go
- **Maintainers / activity:** Very active, production-proven
- **Status:** Study (borrow concepts)

#### Key Findings
- Durable execution engine with retries, timers, approvals, compensation, and replay.
- Ideal for long-running business workflows.
- Not needed as a runtime dependency inside Cursor, but its principles inspire our state machine.

#### CTH Usage
- Borrow deterministic state, approval, retry, and recovery patterns.
- Do not adopt as service in v1.

#### Evidence
- Research session data.

---

### 2. dependency-cruiser
- **Repo:** https://github.com/sverweij/dependency-cruiser
- **License:** MIT
- **Primary language:** JavaScript/TypeScript
- **Maintainers / activity:** Active
- **Status:** Adopt

#### Key Findings
- Determines allowed/disallowed dependencies and detects circular dependencies.
- Exit-code based, configurable via rules.
- Works on JavaScript/TypeScript and can support other languages with parsers.

#### CTH Usage
- Add to System Gate v2 as a fitness function for architecture boundaries and circular dependency checks.

#### Evidence
- Hybrid architecture research 2026-08-20.

---

### 3. eslint-plugin-boundaries
- **Repo:** https://github.com/javierbrea/eslint-plugin-boundaries
- **License:** MIT
- **Primary language:** JavaScript/TypeScript
- **Maintainers / activity:** Active
- **Status:** Adopt

#### Key Findings
- Enforces module boundaries and import rules via ESLint.
- Useful for TypeScript/Next.js projects.
- Deterministic, IDE-native (runs as ESLint rule).

#### CTH Usage
- Use for frontend/Node boundary enforcement as part of System Gate v2.

#### Evidence
- Hybrid architecture research 2026-08-20.

---

### 4. xstate
- **Repo:** https://github.com/statelyai/xstate
- **License:** MIT
- **Primary language:** TypeScript
- **Maintainers / activity:** Active
- **Status:** Optional (defer)

#### Key Findings
- Formal finite state machine library for TypeScript.
- Could model our team phases and transitions.
- Our current state ledger is simple JSON; xstate may be overkill for v1.

#### CTH Usage
- Consider if our custom state machine becomes complex. Defer until evidence.

#### Evidence
- Hybrid architecture research 2026-08-20.

---

### 5. Restate
- **Repo:** https://github.com/restatedev/restate
- **License:** Apache-2.0
- **Primary language:** Rust
- **Maintainers / activity:** Active
- **Status:** Exclude (not needed for CTH)

#### Key Findings
- Durable distributed services; combines RPC and workflows.

#### CTH Usage
- Not needed for an IDE-native harness.

#### Evidence
- Research session data.

---

### 6. Hatchet
- **Repo:** https://github.com/hatchet-dev/hatchet
- **License:** Apache-2.0
- **Primary language:** Go
- **Status:** Exclude

#### Key Findings
- Distributed agent/background task orchestration.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 7. Inngest
- **Repo:** https://github.com/inngest/inngest
- **License:** Verify
- **Status:** Exclude

#### Key Findings
- Event-driven durable functions.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 8. Prefect
- **Repo:** https://github.com/PrefectHQ/prefect
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Exclude

#### Key Findings
- Python data/workflow orchestration.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 9. Kestra
- **Repo:** https://github.com/kestra-io/kestra
- **License:** Apache-2.0
- **Primary language:** Java
- **Status:** Exclude

#### Key Findings
- Declarative orchestration with YAML workflows.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 10. n8n
- **Repo:** https://github.com/n8n-io/n8n
- **License:** Fair-code / Sustainable Use License (verify)
- **Primary language:** TypeScript
- **Status:** Exclude

#### Key Findings
- Visual workflow automation, AI integrations.
- License may restrict commercial embedding.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 11. Dagster
- **Repo:** https://github.com/dagster-io/dagster
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Exclude

#### Key Findings
- Data/asset orchestration.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 12. Airflow
- **Repo:** https://github.com/apache/airflow
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Exclude

#### Key Findings
- Scheduled data pipelines.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 13. Camunda
- **Repo:** https://github.com/camunda/camunda
- **License:** Apache-2.0
- **Primary language:** Java
- **Status:** Exclude

#### Key Findings
- BPMN business process automation with human approvals.

#### CTH Usage
- Not needed; borrow approval concept only.

#### Evidence
- Research session data.

---

### 14. Celery
- **Repo:** https://github.com/celery/celery
- **License:** BSD
- **Primary language:** Python
- **Status:** Exclude (for harness)

#### Key Findings
- Distributed task queue for Python.
- Useful for target applications, not for CTH itself.

#### CTH Usage
- Not part of harness; may appear in target FastAPI apps.

#### Evidence
- Research session data.

---

### 15. Dagu
- **Repo:** https://github.com/yohamta/dagu
- **License:** MIT
- **Status:** Exclude

#### Key Findings
- YAML-defined workflow scheduling.

#### CTH Usage
- Not needed.

#### Evidence
- Research session data.

---

### 16. Windmill
- **Repo:** https://github.com/windmill-labs/windmill
- **License:** AGPL-3.0
- **Primary language:** TypeScript/Rust
- **Status:** Exclude (AGPL risk)

#### Key Findings
- Developer-oriented workflows and scripts.

#### CTH Usage
- Not needed; AGPL risk.

#### Evidence
- Research session data.

---

## Architecture Boundary Enforcement Strategy

Our System Gate v2 will use two layers:

| Layer | Tool | Purpose |
|-------|------|---------|
| Static dependency checks | dependency-cruiser | Circular dependencies, module boundaries |
| IDE-native import rules | eslint-plugin-boundaries | TypeScript/Next.js import enforcement |

Both are deterministic and produce exit codes / lint failures, not LLM judgment.

## State Machine Strategy

Our team workflow state machine remains a simple JSON ledger (`.team/state.json`) with append-only audit logs. We deliberately avoid heavy state-machine libraries in v1 because:

- CTH state transitions are simple and sequential.
- Human approval is a blocking step outside the model.
- A formal library adds dependency overhead with little benefit now.

We may revisit `xstate` if field evidence shows our state logic becomes complex.

## Decision Summary
- Adopt dependency-cruiser and eslint-plugin-boundaries for architecture gates.
- Borrow Temporal’s durable state/approval/retry patterns.
- Defer xstate unless complexity demands it.
- Exclude all other workflow engines as unnecessary for CTH v1.

## Sources
- Hybrid architecture research 2026-08-20.
- Research session data.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial workflow and orchestration category research |
