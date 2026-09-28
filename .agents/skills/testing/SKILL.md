---
name: testing
description: Plan and execute verification of application changes against User Stories, Acceptance Criteria, Test Matrices, and Business Rules.
---

# Testing Skill — Verification & Quality Assurance

## Objective
Establish rigorous, evidence-based verification that changed behavior satisfies documented requirements, preserves business invariants, and introduces no regressions.

---

## 1. Pre-Testing Checklist

Before declaring any feature or bugfix complete:
1. **Identify the Applicable User Story**: Open [`docs/user-stories/US-XXX-name.md`](../../../docs/user-stories/).
2. **Review Acceptance Criteria (ACs)**: Extract the Gherkin scenarios (`Given-When-Then`).
3. **Review Test Cases**: Extract Happy Path, Validation/Error, and Edge Case scenarios.
4. **Identify Affected Business Rules**: Verify against [`docs/rules/BUSINESS_RULES.md`](../../../docs/rules/BUSINESS_RULES.md).
5. **Identify Affected Flow**: Confirm navigation path in [`docs/flows/navigation-wireframe.md`](../../../docs/flows/navigation-wireframe.md).

---

## 2. Verification Execution

### A. Static & Type Verification
Execute the TypeScript compiler to catch interface regressions and type mismatches:
```bash
./node_modules/.bin/tsc --noEmit
```
*Criteria*: Zero errors permitted.

### B. Functional & UI Verification
1. Launch local test server (`npx expo start --web` or Vite showcase).
2. Walk through the exact steps defined in the User Story test cases:
   - **Entry Point**: Can user reach the screen from normal navigation?
   - **Happy Path**: Does valid submission create/update records accurately?
   - **Negative / Edge**: Are invalid amounts, empty inputs, or edge values handled gracefully?
   - **Visual Formatting**: Are currency amounts formatted with period thousand separators (`Rp 150.000`)?
   - **Exit / Return**: Does tapping `Batal` or backdrop return cleanly to the parent view?

### C. Financial & Ledger Verification
For any transaction or balance modification:
- Confirm source account balance changed by the exact integer amount.
- Confirm split-bill portions route to `Virtual_Pocket: Piutang` and NOT Income.
- Confirm historical transaction integrity is preserved.

---

## 3. Test Evidence Report Format

Always present verification outcomes using this standardized format:

```markdown
### Verification Summary for US-XXX: [Story Title]

| Test Case | Scenario Description | Expected Outcome | Actual Outcome | Status |
|---|---|---|---|---|
| **TC-01** | Happy Path: [Action] | [Expected] | [Observed] | **PASS** |
| **TC-02** | Validation: [Invalid input] | Error banner / blocked | Validation displayed | **PASS** |
| **TC-03** | Flow Exit: Tap Batal | Dismisses modal, no change | Returned to parent | **PASS** |
| **TC-04** | Financial Invariant | Zero skew to monthly income | Verified via query | **PASS** |

**Static Verification**: `tsc --noEmit` passed with 0 errors.
**Unverified Checks**: [Explicitly mention any hardware/push checks that could not be run locally]
```

---

## 4. Post-Verification Updates

Once all test cases pass:
1. Update the **Verification Status** field in [`docs/user-stories/US-XXX-name.md`](../../../docs/user-stories/) to `Verified`.
2. Ensure the entry under `## [Unreleased]` in [`docs/CHANGELOG.md`](../../../docs/CHANGELOG.md) accurately reflects the verified change.
