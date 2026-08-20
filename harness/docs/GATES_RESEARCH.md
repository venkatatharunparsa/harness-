# Production Gates Research: System Gate, Sentinel, and Additional Gates

**Date:** 2026-08-20  
**Status:** Evidence-based findings from Research Sprint 3  
**Method:** Deep research with primary sources, official documentation, open-source tool docs, and production-readiness frameworks.

## 1. Executive Summary

Current gates are necessary but insufficient:

- **System Gate** validates architecture structure and evidence, but not production properties like multi-tenancy, auth coverage, observability, deployment readiness, and compliance.
- **Sentinel** wraps gitleaks + opengrep + trivy, which cover secrets, SAST, dependencies, IaC, and containers, but miss supply chain provenance, DAST, runtime security, API authz correctness, mobile security, secrets management, and license risk.
- Additional **production-readiness gates** are needed for reliability, performance, observability, deployment, scalability, and cost.

Our solution is to extend both gates with **deterministic evidence-based checks** — no LLM judgment — using exit codes, thresholds, and files on disk.

## 2. Research Questions & Answers

### Q1. Is System Gate production-ready?

**Answer:** No. The schema-based architecture gate verifies structural completeness and consistency but misses production-readiness properties like multi-tenancy isolation, auth coverage, rate limiting, PII redaction, observability, deployment readiness, and compliance.

**Confidence:** VERIFIED.

**Key implications:**

- Keep existing schema validation.
- Add a fitness-function layer with deterministic checks.
- Use tools like Semgrep, sqlparse, curl, dependency scanners, and config validators.
- Never use LLM judgment to determine architecture fitness.

### Q2. Is Sentinel production-ready for commercial SaaS?

**Answer:** Not alone. gitleaks + opengrep + trivy cover core static scanning, but miss:

- Dependency/license risk.
- Supply chain provenance.
- Dynamic application security testing.
- Runtime security.
- API authorization correctness.
- Mobile security.
- Cloud/IaC posture management.
- Secrets rotation and management.

**Confidence:** VERIFIED.

**Key implications:**

- Extend Sentinel into staged gates:
  - Core stage: gitleaks, opengrep, trivy.
  - Supply-chain stage: osv-scanner/grype, slsa-verifier/cosign, SBOM.
  - Dynamic stage: OWASP ZAP/nuclei, zaproxy/42crunch, falco/tracee.
  - Platform stage: mobSF/qark for mobile, checkov/terrascan for IaC.
  - Secrets audit stage: vault/gopass policies.
- Each stage produces exit codes and ledger evidence.

### Q3. What additional production-readiness gates are needed?

**Answer:** Six gates beyond architecture and security:

- Reliability: SLOs + error budgets + burn-rate alerts.
- Performance: performance budgets + load testing + latency percentiles.
- Observability: structured logging + distributed tracing + metrics + alerting.
- Deployment: CI/CD automation + rollback/canary + deployment frequency.
- Scalability: auto-scaling + load balancing + capacity planning.
- Cost: cost budgets + resource utilization + cost optimization.

**Confidence:** VERIFIED.

**Key implications:**

- Add a new `.production-gates/` layer.
- Each gate uses deterministic config files + check scripts.
- `team_validate_phase` invokes these for QA/Release phases.

## 3. Consolidated Evidence Table

| # | Claim | Confidence | Source Type |
|---|---|---|---|
| 1 | System Gate schema-only is insufficient | VERIFIED | SaaS checklists, production frameworks |
| 2 | Fitness functions can validate architectural properties | VERIFIED | C4 model, ArchUnit, fitness function docs |
| 3 | Sentinel core stack is insufficient for commercial SaaS | VERIFIED | gitleaks/opengrep/trivy docs |
| 4 | Supply chain needs SBOM + provenance | VERIFIED | SLSA, sigstore, osv-scanner docs |
| 5 | DAST + runtime + API authz + mobile + IaC needed | VERIFIED | OWASP, falco, mobSF, checkov docs |
| 6 | Reliability gate uses SLO + error budgets + burn-rate alerts | VERIFIED | Grafana, OpenObserve, SRE docs |
| 7 | Performance gate uses budgets + load tests + latency percentiles | VERIFIED | k6, performance testing docs |
| 8 | Observability gate uses logging + tracing + metrics + alerting | VERIFIED | OpenTelemetry, Prometheus, Grafana |
| 9 | Deployment gate uses CI/CD + rollback + canary | VERIFIED | ArgoCD, Flux, deployment frameworks |
| 10 | Scalability gate uses auto-scaling + load balancing + capacity | VERIFIED | Kubernetes HPA, KEDA, NGINX |
| 11 | Cost gate uses budgets + utilization + optimization | VERIFIED | Kubecost, Prometheus |

## 4. Final Gate Architecture

### System Gate v2
- Layer 1: Schema validation (existing `.system-gate/architecture.json`).
- Layer 2: Fitness functions (new `.system-gate/fitness-functions.json`).
- Each fitness function uses deterministic tool and returns PASS/FAIL/ERROR.

### Sentinel v2
- Stage 1 (always): gitleaks + opengrep + trivy.
- Stage 2 (build): osv-scanner/grype + SBOM + slsa-verifier.
- Stage 3 (pre-release): OWASP ZAP/nuclei + API authz + falco.
- Stage 4 (platform): mobSF/qark + checkov/terrascan.
- Stage 5 (periodic): secrets rotation/access audit.
- All stages write to `.sentinel/ledger/` and contribute to `ship_readiness`.

### Production Gates
- `.production-gates/` with six subdirectories.
- Each gate has config + check script + evidence artifacts.
- Invoked in QA and Release phases.

## 5. Open Questions & Risks

- ArchUnit is Java-specific; need Python/TS/Dart equivalents for fitness functions.
- SLO thresholds require historical data; start with defaults and tune after dogfood.
- Dynamic/runtime security stages require staging environment and may slow release.
- Mobile DAST and API authz testing may need dedicated devices/emulators.
- Cost budgets depend on business stage and may be advisory in v1.

## 6. Decisions Influenced

- System Gate will be extended with fitness functions.
- Sentinel will become stage-based.
- A new production-gates layer will cover reliability/performance/observability/deployment/scalability/cost.
- All gates remain deterministic and evidence-based.

## 7. Appendix: Primary Sources

- SLO guide: https://grafana.com/blog/slos-a-guide-to-setting-and-benefiting-from-service-level-objectives/
- SLOs from logs/metrics: https://www.elastic.co/observability-labs/blog/service-level-objectives-slos-logs-metrics
- SLOs in 2026: https://openobserve.ai/blog/set-meaningful-slos/
- Security gates with SLOs: https://devsecopsschool.com/blog/security-gates/
- Scalable web app checklist: https://wearepresta.com/from-prototype-to-production-scalable-web-apps-checklist/
- SaaS technical checklist: https://www.reddit.com/r/SaaS/comments/1r6bfl5/technical_saas_checklist_things_youll_regret_not/
- SCA/supply chain: https://www.veracode.com/blog/software-supply-chain-risk-management/
- DAST: https://www.ox.security/blog/dynamic-application-security-testing-dast/
- Open source security tools: https://orca.security/resources/blog/open-source-application-security-tools/

Full detailed findings and source links are preserved in the research session history.
