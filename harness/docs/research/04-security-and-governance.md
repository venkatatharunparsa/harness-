# Security and Governance Research

**Category:** Security scanning, supply chain, runtime, governance, and guardrails  
**Status:** Living document  
**Date:** 2026-08-20

## Overview
This file documents security and governance tools evaluated for CTH. Our Sentinel gate is the security enforcement layer. We started with core static scanning and are extending to supply-chain, dynamic/runtime, platform-specific, and secrets management stages. Every tool here must be deterministic and evidence-based, never relying on LLM judgment.

## Repositories Evaluated

### 1. gitleaks
- **Repo:** https://github.com/gitleaks/gitleaks
- **License:** MIT
- **Primary language:** Go
- **Maintainers / activity:** Active
- **Status:** Adopt (Sentinel core)

#### Key Findings
- Detects hardcoded secrets in Git repos and files.
- Fast, local, exit-code driven.
- Part of current Sentinel core.

#### CTH Usage
- Keep in Sentinel core for secrets detection.
- Use with `detect` / `protect` modes and evidence output.

#### Evidence
- Sentinel conventions and research sessions.

---

### 2. opengrep
- **Repo:** https://github.com/opengrep/opengrep
- **License:** AGPL-3.0 (verify)
- **Primary language:** TypeScript/OCaml? (verify)
- **Maintainers / activity:** Active
- **Status:** Adopt with license review

#### Key Findings
- Static analysis engine used in existing Sentinel core.
- Covers SAST patterns for multiple languages.
- License may have AGPL implications; must verify for commercial SaaS.

#### CTH Usage
- Keep as SAST core in Sentinel v2.
- Verify license before final production reliance.

#### Evidence
- Sentinel docs, research sessions.

---

### 3. trivy
- **Repo:** https://github.com/aquasecurity/trivy
- **License:** Apache-2.0
- **Primary language:** Go
- **Maintainers / activity:** Active
- **Status:** Adopt (Sentinel core)

#### Key Findings
- Comprehensive scanner for OS packages, language dependencies, IaC, container images.
- Exit-code driven, produces JSON evidence.
- Does not cover dynamic/runtime/API authz/mobile DAST/secrets management.

#### CTH Usage
- Keep in Sentinel core for dependency/IaC/container scanning.

#### Evidence
- Sentinel docs, research sessions.

---

### 4. Semgrep
- **Repo:** https://github.com/semgrep/semgrep
- **License:** LGPL-2.1
- **Primary language:** OCaml/Python
- **Maintainers / activity:** Active
- **Status:** Adopt (Sentinel v2 extension)

#### Key Findings
- Extensible static analysis with custom rules.
- Strong ecosystem and language support.
- Can complement opengrep with custom patterns.
- LGPL is acceptable for external CLI usage, not embedding.

#### CTH Usage
- Add to Sentinel v2 for custom FastAPI/Node/Flutter rules.
- Run as external CLI process.

#### Evidence
- Gates research 2026-08-20.

---

### 5. osv-scanner
- **Repo:** https://github.com/google/osv-scanner
- **License:** Apache-2.0
- **Primary language:** Go
- **Status:** Adopt (Sentinel supply-chain stage)

#### Key Findings
- Scans dependencies against OSV vulnerability database.
- Generates SBOM and can identify license issues.

#### CTH Usage
- Add to Sentinel supply-chain stage.

#### Evidence
- Gates research 2026-08-20.

---

### 6. slsa-verifier / cosign
- **Repo:** https://github.com/slsa-framework/slsa-verifier / https://github.com/sigstore/cosign
- **License:** Apache-2.0
- **Primary language:** Go
- **Status:** Adopt (Sentinel supply-chain stage)

#### Key Findings
- slsa-verifier checks provenance metadata.
- cosign signs and verifies artifacts.
- Deterministic exit-code checks.

#### CTH Usage
- Add to Sentinel supply-chain stage for provenance verification.

#### Evidence
- Gates research 2026-08-20.

---

### 7. OWASP ZAP / nuclei
- **Repo:** https://github.com/zaproxy/zaproxy / https://github.com/projectdiscovery/nuclei
- **License:** Apache-2.0 / MIT
- **Primary language:** Java/Go
- **Status:** Optional (Sentinel dynamic stage)

#### Key Findings
- ZAP provides automated DAST for running apps.
- nuclei is a fast template-based scanner.
- Useful for runtime API/web vulnerability discovery.
- Adds CI/CD time; requires staging environment.

#### CTH Usage
- Add to Sentinel dynamic stage for pre-release, opt-in via deploy intent.

#### Evidence
- Gates research 2026-08-20.

---

### 8. falco / tracee
- **Repo:** https://github.com/falcosecurity/falco / https://github.com/aquasecurity/tracee
- **License:** Apache-2.0
- **Primary language:** C/Go
- **Status:** Optional (Sentinel runtime stage)

#### Key Findings
- Runtime security monitoring and anomaly detection.
- Requires instrumentation and privileged access.

#### CTH Usage
- Add to Sentinel runtime stage in staging environments.

#### Evidence
- Gates research 2026-08-20.

---

### 9. mobSF / qark
- **Repo:** https://github.com/MobSF/Mobile-Security-Framework-MobSF / https://github.com/linkedin/qark
- **License:** GPL-3.0 / Apache-2.0 (verify)
- **Primary language:** Python/Java
- **Status:** Optional (Sentinel mobile stage)

#### Key Findings
- Mobile-specific static and dynamic analysis.
- Useful for Flutter/React Native app builds.

#### CTH Usage
- Add to Sentinel platform stage for mobile app releases.

#### Evidence
- Gates research 2026-08-20.

---

### 10. checkov / terrascan
- **Repo:** https://github.com/bridgecrewio/checkov / https://github.com/tenable/terrascan
- **License:** Apache-2.0 / Apache-2.0
- **Primary language:** Python/Go
- **Status:** Adopt (Sentinel IaC stage)

#### Key Findings
- Static analysis for Infrastructure-as-Code.
- Prevents cloud misconfigurations.
- Exit-code driven with JSON evidence.

#### CTH Usage
- Add to Sentinel IaC/platform stage.

#### Evidence
- Gates research 2026-08-20.

---

### 11. gopass / vault
- **Repo:** https://github.com/gopasspw/gopass / https://github.com/hashicorp/vault
- **License:** MIT / MPL-2.0
- **Primary language:** Go
- **Status:** Optional (Sentinel secrets management stage)

#### Key Findings
- Secrets management, rotation, access audit.
- Vault is more complex; gopass is simpler.

#### CTH Usage
- Add to Sentinel secrets audit stage.

#### Evidence
- Gates research 2026-08-20.

---

### 12. OpenShell
- **Repo:** https://github.com/open-shell/openshell (verify URL)
- **License:** Verify
- **Primary language:** Rust/Go?
- **Status:** Study

#### Key Findings
- Policy-enforced autonomous agent runtime.
- Declarative filesystem/network/process policies.

#### CTH Usage
- Borrow policy enforcement ideas for our `preToolUse`/shell hooks.

#### Evidence
- Research session data.

---

### 13. Pipelock
- **Repo:** https://github.com/pipelock/pipelock
- **License:** Verify
- **Primary language:** Go/Rust?
- **Status:** Study

#### Key Findings
- MCP-aware egress/DLP firewall.

#### CTH Usage
- Borrow boundary enforcement concepts; not needed as dependency.

#### Evidence
- Research session data.

---

### 14. LlamaFirewall
- **Repo:** Verify URL
- **License:** Verify
- **Status:** Study

#### Key Findings
- Prompt injection, alignment, and code security defense.

#### CTH Usage
- Borrow input/output validation ideas; not core.

#### Evidence
- Research session data.

---

### 15. NeMo Guardrails
- **Repo:** https://github.com/NVIDIA/NeMo-Guardrails
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Study

#### Key Findings
- Programmable conversational and tool guardrails.

#### CTH Usage
- Possibly later for agent-specific guardrails; not v1.

#### Evidence
- Research session data.

---

### 16. Guardrails AI
- **Repo:** https://github.com/guardrails-ai/guardrails
- **License:** Apache-2.0
- **Status:** Optional

#### Key Findings
- Structured output validation and schemas.

#### CTH Usage
- May be used for artifact schema validation if needed.

#### Evidence
- Research session data.

---

### 17. Presidio
- **Repo:** https://github.com/microsoft/presidio
- **License:** MIT
- **Primary language:** Python
- **Status:** Optional

#### Key Findings
- PII detection and anonymization.

#### CTH Usage
- Use in logging pipeline to prevent sensitive data leakage.

#### Evidence
- Research session data.

---

### 18. SkillSpector
- **Repo:** https://github.com/NVIDIA/SkillSpector
- **License:** Apache-2.0
- **Primary language:** Python
- **Status:** Optional

#### Key Findings
- Scans skills/plugins for prompt injection, malicious code, data exfiltration.

#### CTH Usage
- If we adopt a skill marketplace, use SkillSpector to scan skills.

#### Evidence
- Research session data.

---

### 19. Garak / PyRIT
- **Repo:** https://github.com/leondz/garak / https://github.com/Azure/PyRIT
- **License:** Apache-2.0 / MIT
- **Status:** Optional

#### Key Findings
- LLM vulnerability scanning (garak) and red-team testing (PyRIT).

#### CTH Usage
- Later for agent security testing.

#### Evidence
- Research session data.

---

## Security Stage Architecture (Sentinel v2)

| Stage | Tools | Evidence |
|-------|-------|----------|
| Core (always) | gitleaks, opengrep, trivy | `.sentinel/ledger/` |
| Supply chain | osv-scanner, slsa-verifier, cosign | SBOM + provenance |
| Dynamic | OWASP ZAP / nuclei | DAST reports |
| Runtime | falco / tracee | Runtime alerts |
| Platform | mobSF / qark, checkov / terrascan | Mobile/IaC reports |
| Secrets management | gopass / vault audit | Secret lifecycle audit |

All stages are deterministic and exit-code-based. No LLM judgment.

## Decision Summary
- Sentinel v2 becomes stage-based.
- Core static scanning remains.
- Add supply-chain, dynamic, runtime, platform, and secrets stages.
- Extend with Semgrep for custom rules.
- Keep opengrep but verify license.
- Use Presidio in logs.
- Optional: SkillSpector if we adopt skills.

## Sources
- Gates research 2026-08-20.
- Sentinel conventions and earlier research sessions.

## Revision History
| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1 | 2026-08-20 | Human lead | Initial security and governance category research |
