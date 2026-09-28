---
name: debugging
description: Systematically diagnose, isolate, and resolve bugs, runtime exceptions, and logic errors in the Stir application.
---

# Debugging Skill — Stir Diagnostics & Troubleshooting

## Objective
Identify the underlying root cause of defects before proposing or applying fixes. Avoid superficial error suppression, arbitrary timeouts, or speculative edits.

---

## 1. Diagnostic Protocol

Always follow this 5-step methodology:

1. **Reproduction & Evidence Gathering**:
   - Reproduce the error with deterministic steps.
   - Capture exact error messages, stack traces, and console logs.
   - Inspect network request/response payloads in DevTools.
2. **Component & Layer Isolation**:
   - Determine which layer originated the failure:
     - Frontend State / React Component (`components/`, `app/`)
     - Business Engine / Calculation (`lib/socialEngine.ts`, `lib/transactions.ts`, `lib/reportEngine.ts`)
     - Local Storage / Session (`@react-native-async-storage/async-storage`)
     - Backend / RPC / RLS (`supabase/`)
3. **Inspect Active State & Schema**:
   - Inspect the active database schema (`supabase/schema.sql`, `supabase/migrations/`).
   - Inspect active RLS policies (`auth.uid() = user_id`).
   - Check input parameter types and database constraints.
4. **Formulate Root Cause Hypothesis**:
   - State the exact root cause clearly before writing a fix.
   - Distinguish root cause from secondary symptoms.
5. **Targeted Correction & Regression Check**:
   - Apply the smallest coherent fix addressing the root cause.
   - Re-run the reproduction to confirm the issue is resolved.
   - Verify adjacent flows to ensure zero regressions.

---

## 2. Project Debugging Commands

- **Type Checking**:
  ```bash
  ./node_modules/.bin/tsc --noEmit
  ```
- **Expo Bundler / Native Web**:
  ```bash
  npx expo start --web
  ```
- **Vite Desktop Showcase**:
  ```bash
  npm run dev
  ```
- **Ad-Hoc Verification Scripts**:
  ```bash
  # Check scripts/ directory for focused engine assertions
  node scripts/<script-name>.js
  ```

---

## 3. Strict Negative Constraints

- **No Timing Hacks**: Never use arbitrary `setTimeout(..., 500)` or delayed promises to hide race conditions. Fix state lifecycle or database locking properly.
- **No Swallowed Errors**: Never wrap faulty code in empty `try {} catch (e) {}` blocks or silence console warnings.
- **No Speculative Refactoring**: Do not refactor unrelated files or dependencies while investigating a bug.
- **No Mock Fallbacks in Production Code**: Do not inject mock data into production branches to bypass real query errors; fix the query or schema mismatch.
