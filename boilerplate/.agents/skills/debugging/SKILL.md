---
name: debugging
description: Systematically diagnose, isolate, and resolve bugs, runtime errors, and unexpected behavior.
---

# Debugging Skill — Diagnostics & Defect Resolution

## Objective
Identify and resolve the underlying root cause of bugs before modifying code. Avoid superficial error suppression, arbitrary timeouts, or speculative edits.

---

## 1. 5-Step Diagnostic Protocol

1. **Reproduce Deterministically**: Capture logs, reproduction steps, and exact error payloads.
2. **Isolate the Layer**: Pinpoint whether the defect originates in UI state, business engine, network/API, or database.
3. **Inspect Active State & Configuration**: Check types, schema constraints, and environment settings.
4. **State Root Cause**: Articulate the exact cause before writing code.
5. **Targeted Fix & Verification**: Apply the minimal coherent fix and verify both reproduction and adjacent flows.

---

## 2. Prohibited Workarounds

- **No Timing Delays**: Do not use arbitrary `setTimeout` delays to bypass race conditions.
- **No Swallowed Exceptions**: Never wrap code in empty `try {} catch {}` blocks.
- **No Mock Fallbacks in Production Code**: Fix the underlying query or data mismatch rather than masking errors with fake fallback data.
