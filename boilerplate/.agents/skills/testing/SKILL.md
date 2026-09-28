---
name: testing
description: Plan and execute verification of changes against User Stories, Acceptance Criteria, Test Matrices, and Business Rules.
---

# Testing Skill — Verification Protocol

## Objective
Establish rigorous, evidence-based proof that code changes satisfy documented requirements without introducing regressions.

---

## 1. Verification Protocol

1. **Inspect User Story**: Open [`docs/user-stories/`](../../../docs/user-stories/) and retrieve Gherkin Acceptance Criteria.
2. **Review Test Cases**: Prepare Happy Path, Error/Validation, and Edge Case scenarios.
3. **Execute Static Checks**: Run linter and type-checker (`tsc --noEmit` or equivalent).
4. **Execute Functional / Browser Checks**: Verify the actual running user flow end-to-end.
5. **Output Standard Evidence Table**:

```markdown
### Verification Summary for US-XXX: [Title]

| Test Case | Scenario | Expected | Actual | Status |
|---|---|---|---|---|
| **TC-01** | Happy Path | [Expected] | [Observed] | **PASS** |
| **TC-02** | Validation | [Expected] | [Observed] | **PASS** |
| **TC-03** | Exit / Cancel | [Expected] | [Observed] | **PASS** |

**Static Verification**: Passed with 0 errors.
```

6. **Update Story Status**: Mark story status as `Verified` once all test cases pass.
