<!-- This file is a template. Do not delete. Reusable template for individual findings AUD-<DOMAIN>-<ID>. -->

# Finding: [AUD-<DOMAIN>-<ID>] — [Short Descriptive Title]

## Metadata

| Field | Value | Allowed / Expected Values |
|---|---|---|
| **Finding ID** | `AUD-<DOMAIN>-<ID>` | Format: `AUD-SEC-001`, `AUD-PRIV-001`, `AUD-SC-001`, `AUD-LEG-001`, `AUD-PLAT-001`, `AUD-REG-001`, `AUD-ADV-001`, `AUD-REL-001` |
| **Status** | `[STATUS]` | `PASS` \| `WARN` \| `REVIEW` \| `BLOCK` \| `NOT_APPLICABLE` \| `NOT_VERIFIED` |
| **Severity** | `[SEVERITY]` | `CRITICAL` \| `HIGH` \| `MEDIUM` \| `LOW` \| `INFO` |
| **Domain** | `[DOMAIN]` | `Security` \| `Privacy` \| `Supply Chain` \| `Legal/IP` \| `Platform Compliance` \| `Regional Considerations` \| `Adversarial Testing` \| `Release Verification` |
| **Source Skill** | `[SOURCE_SKILL]` | e.g. `.agents/skills/audit-security/SKILL.md` |
| **Resolution State** | `[RESOLUTION_STATE]` | `OPEN` \| `IN_PROGRESS` \| `RESOLVED` \| `ACCEPTED_RISK` \| `WONT_FIX` |
| **Date Identified** | `[YYYY-MM-DD]` | ISO date string |
| **Auditor** | `[AUDITOR_ID]` | Agent ID or Engineer Persona |

---

## Finding Description

[Provide a clear, detailed, and objective explanation of the deficiency, gap, or vulnerability identified. Explain what was observed versus what was expected according to project standards or regulatory baselines.]

---

## Evidence

*Exact file paths, line numbers, or verbatim CLI command and terminal outputs demonstrating the issue:*

```text
File: path/to/file.ext
Lines: 42-48
Code / Output:
[Verbatim snippet or CLI command output showing exact reproduction]
```

---

## Impact

[Describe the concrete consequences if this finding is not remediated. Consider security risk (e.g. data breach, unauthorized privilege escalation), user privacy violations, store rejection risk (Apple/Google), regulatory penalties (e.g. UU PDP, GDPR), or supply chain compromise.]

---

## Recommendation

[Provide actionable, step-by-step instructions for the developer to resolve this finding. Include sample code snippets, schema adjustments, or configuration changes where applicable. Follow the minimal-change principle.]

---

## Verification Method

*How to independently verify that this finding has been completely remediated:*

```bash
# Exact CLI command or inspection procedure:
[Command to run that confirms the fix]
```

*Expected Verification Output:*
- [What the terminal or code inspector should observe once properly resolved.]

---

## References

- [External standard, regulation, or guideline: e.g. OWASP MASVS v2.0 §MASVS-AUTH-1]
- [Apple App Review Guideline / Google Play Policy reference]
- [Statutory law: e.g. UU No. 27/2022 Pasal 20]
- [Internal reference: `docs/SECURITY_MODEL.md §X`]

---

## Resolution Log

| Date | State Transition | Actor | Remediation Notes / Git Commit |
|---|---|---|---|
| [YYYY-MM-DD] | OPEN | [Auditor] | Finding recorded during audit execution. |
| [YYYY-MM-DD] | RESOLVED | [Developer] | [Commit hash & explanation of fix verified.] |
