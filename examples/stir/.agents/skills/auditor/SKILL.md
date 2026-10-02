---
name: auditor
description: Master audit orchestrator that evaluates project scope, sequentially guides domain skill execution, and compiles consolidated audit reports.
---

# Auditor Skill — Assurance Orchestration & Verification Protocol

## Objective
Coordinate end-to-end multi-platform assurance audits by evaluating project scope, sequentially executing domain audit skills, and compiling consolidated reports. Never execute code fixes or remediations automatically without explicit developer instruction.

---

## 1. Orchestration Reading Protocol

1. **Ingest Scope & Architecture**:
   - Read [`docs/audit/AUDIT_SCOPE.md`](../../../docs/audit/AUDIT_SCOPE.md) for project parameters and `audit_modules` flags.
   - Read [`docs/PRD.md`](../../../docs/PRD.md), [`docs/ARCHITECTURE.md`](../../../docs/ARCHITECTURE.md), and [`docs/SECURITY_MODEL.md`](../../../docs/SECURITY_MODEL.md).
2. **Sequential Skill Execution**:
   Read and execute applicable domain skills in sequence based on `audit_modules` flags:
   - If `audit_modules.security: true` ➔ Read [`.agents/skills/audit-security/SKILL.md`](../audit-security/SKILL.md)
   - If `audit_modules.privacy: true` ➔ Read [`.agents/skills/audit-privacy/SKILL.md`](../audit-privacy/SKILL.md)
   - If `audit_modules.supply_chain: true` ➔ Read [`.agents/skills/audit-supply-chain/SKILL.md`](../audit-supply-chain/SKILL.md)
   - If `audit_modules.legal_ip: true` ➔ Read [`.agents/skills/audit-legal-ip/SKILL.md`](../audit-legal-ip/SKILL.md)
   - If `audit_modules.platform: true` (and mobile platforms active) ➔ Read [`.agents/skills/audit-platform/SKILL.md`](../audit-platform/SKILL.md)
   - If `audit_modules.regional: true` (and target regions configured) ➔ Read [`.agents/skills/audit-regional/SKILL.md`](../audit-regional/SKILL.md)
   - If `audit_modules.adversarial: true` ➔ Read [`.agents/skills/audit-adversarial/SKILL.md`](../audit-adversarial/SKILL.md)
   - If `audit_modules.release: true` (and release build pending) ➔ Read [`.agents/skills/audit-release/SKILL.md`](../audit-release/SKILL.md)
3. **Record Individual Findings**:
   - For each defect or vulnerability discovered, instantiate [`docs/audit/findings/FINDING_TEMPLATE.md`](../../../docs/audit/findings/FINDING_TEMPLATE.md) as `docs/audit/findings/AUD-<DOMAIN>-<ID>.md`.
4. **Compile Consolidated Audit Report**:
   - Synthesize findings into [`docs/audit/AUDIT_REPORT.md`](../../../docs/audit/AUDIT_REPORT.md) using the 6-state status model (`PASS`, `WARN`, `REVIEW`, `BLOCK`, `NOT_APPLICABLE`, `NOT_VERIFIED`). Never use arbitrary numerical scores.
5. **Interactive Remediation Gate**:
   - Present the Executive Summary and blocking items to the developer.
   - **Remediation Guardrail**: Ask the developer which findings to remediate — do not auto-fix without explicit instruction.
