# Stir — Repository Agent Constitution (AGENTS.md)

## Purpose & Scope

This file defines the mandatory working contract for all AI agents (Antigravity, GitHub Copilot, Claude Code, etc.) operating in this repository.

Project documentation is the single source of truth for product behavior and architecture. Implementation must strictly remain consistent with documented specifications.

---

## 1. Project Sources of Truth & Context Routing

Before executing or proposing non-trivial code modifications, inspect the authoritative documentation:

| Domain | File Path | Focus |
|---|---|---|
| **Product Intent & Vision** | [`docs/PRD.md`](docs/PRD.md) | Feature vision, user personas, functional scope |
| **UI & Design System** | [`docs/DESIGN_GUIDELINES.md`](docs/DESIGN_GUIDELINES.md) | Component primitives, typography floor, modal specs, tokens |
| **System Architecture** | [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Tech stack, 3-phase workflow, security, active data model |
| **User Stories & ACs** | [`docs/user-stories/`](docs/user-stories/) | Gherkin acceptance criteria and test matrices |
| **User Flows & Navigation** | [`docs/flows/navigation-wireframe.md`](docs/flows/navigation-wireframe.md) | 16-screen route map, flowcharts, reachability |
| **Business Rules & Schema** | [`docs/rules/BUSINESS_RULES.md`](docs/rules/BUSINESS_RULES.md) | IDR precision, split-bill logic, uang gaib, tenant isolation |
| **Product Changelog** | [`docs/CHANGELOG.md`](docs/CHANGELOG.md) | High-level product and requirement changes |
| **Developer Preferences** | [`DEVELOPER.md`](DEVELOPER.md) | Universal collaboration habits and style constraints |
| **Procedural Skills** | [`.agents/skills/`](.agents/skills/) | Step-by-step guides for frontend, debugging, testing, accounting |
| **Flow Auditor Agent** | [`.github/agents/flow-auditor.md`](.github/agents/flow-auditor.md) | Independent auditor for flow completeness & discovery |
| **Security Model** | [`docs/SECURITY_MODEL.md`](docs/SECURITY_MODEL.md) | Intended security design, threat actors, trust boundaries |
| **Audit Scope & Reports** | [`docs/audit/`](docs/audit/) | Project audit manifest, consolidated reports, findings |
| **Assurance Auditor** | [`.agents/skills/auditor/`](.agents/skills/auditor/SKILL.md) | Master orchestrator for security, privacy, and release assurance |

**Negative Constraint**: Never invent product behavior when documented behavior exists. When requirements, code, and documentation conflict, surface the discrepancy to the user immediately—do not resolve it silently.

---

## 2. Documentation Synchronization Policy (Definition of Done)

Code and documentation must remain synchronized. A task that knowingly leaves documentation inconsistent with implementation is **incomplete**.

Whenever an implementation alters documented behavior, acceptance criteria, or flows:
1. Update the code implementation.
2. Update the affected User Story in [`docs/user-stories/`](docs/user-stories/) (status, ACs, test cases).
3. Update the affected flow diagram in [`docs/flows/`](docs/flows/) if navigation or screen steps changed.
4. Record an entry under `## [Unreleased]` in [`docs/CHANGELOG.md`](docs/CHANGELOG.md).
5. Update the `## Changelog` at the top of any modified documentation file.

*Note*: Internal refactorings, formatting adjustments, and trivial fixes that do not alter documented product behavior do not require changelog entries.

---

## 3. User Story Driven Development

Every user-facing feature or adjustment must tie back to a User Story:
- Identify the target story in [`docs/user-stories/`](docs/user-stories/).
- Review its **Given-When-Then** Acceptance Criteria and Test Matrix before changing code.
- If substantial new product behavior is requested without an existing story, create a new draft story in `docs/user-stories/` before considering the feature done.

---

## 4. User Flow & Reachability Integrity

A feature is **NOT** complete merely because:
- Its screen component exists;
- Its route file is registered;
- Its API/database queries execute;
- It can be opened via a direct localhost URL.

**The Reachability Rule**: Every user-facing screen must have a legitimate, discoverable navigation path from the main app interface.
- Audit all flows: `Discovery → Entry → Navigation → Action → Feedback → Exit/Return`.
- Verify back-button behavior, cancellation actions, and success redirects.
- Run the [`.github/agents/flow-auditor.md`](.github/agents/flow-auditor.md) agent to challenge reachability whenever adding or modifying screens.

---

## 5. UI & Design System Strictness

Implementation must strictly follow [`docs/DESIGN_GUIDELINES.md`](docs/DESIGN_GUIDELINES.md):
- **Centralized Primitives**: Always reuse components from `components/kit/primitives.tsx` (`<Btn>`, `<Card>`, `<Sheet>`, `<Pill>`, `<Label>`, `<Title>`, `<Sub>`). Never write raw `<button>` elements or custom `<div onClick>` containers with inline rounded classes.
- **Button Rounding (`Btn`)**: Main CTA buttons must strictly use `rounded-2xl` (16px). Never apply `rounded-full` to narrow action buttons.
- **Pills & Tabs**: Use `rounded-full` for chips and tab switchers (`flex rounded-full bg-surface-2 p-1`).
- **Side-by-Side Buttons**: Use explicit flex widths (`!w-[28%] shrink-0` for Batal/secondary, `!w-auto flex-1` for primary).
- **Slide-Up Modal Baseline**:
  - Border radius: `rounded-t-[1.6rem]` (25.6px).
  - Dynamic height: `h-auto max-h-[90vh]`.
  - Clean header: No top `✕` close button; tap backdrop or bottom `Batal` button to dismiss.
  - Dismiss button: `tone="ghost"` (`border border-ink/15 text-ink/70`). Never use danger/red for dismissal.
  - Typography: Plus Jakarta Sans (`font-sans`), minimum floor `text-[11px]`, main text `text-[14px]`.
  - Number formatting: Mandatory Indonesian period thousand separators (`id-ID`).
- **Protected Visual Baseline**: Do NOT modify `reference-kit/`. It is a frozen visual baseline.

---

## 6. Business Rules & Financial Integrity

All business rules in [`docs/rules/BUSINESS_RULES.md`](docs/rules/BUSINESS_RULES.md) are authoritative:
- **Currency**: Whole Indonesian Rupiah (`IDR`). No fractions/cents.
- **Social Ledger / Split Bill**: Covered amounts must route into `Virtual_Pocket: Piutang`. Reimbursements must NOT count as Income.
- **Balance Reconciliation ("Uang Gaib")**: Create discrepancy adjustment records; do NOT overwrite historical transactions.
- **Tenant Isolation**: Strictly enforce `auth.uid() = user_id`. Never leak cross-user rows.
- **Secrets**: Never expose Supabase `service_role` keys to client code.

---

## 7. Change Discipline & Non-Destructive Edits

- Understand existing code and active migrations before proposing changes.
- Make the smallest coherent, non-destructive diff.
- Do not create parallel or duplicate implementations of existing abstractions.
- Ask for user confirmation before destructive operations, schema drops, or major library additions.

---

## 8. Verification & Evidence Reporting

Never claim a task is complete or that tests passed without executing verification.
- Run type checks: `./node_modules/.bin/tsc --noEmit`
- Run local server: `npx expo start --web` (or Vite showcase)
- Follow the testing protocol in [`.agents/skills/testing/SKILL.md`](.agents/skills/testing/SKILL.md).
- Report outcomes explicitly as `PASS`, `FAIL`, or `BLOCKED` with supporting evidence.

---

## 9. AI-Generated Software Assurance

Treat all AI-generated code as untrusted implementation output. Never assume AI code is secure, correctly licensed, or original. Unknown provenance must be marked UNKNOWN or REVIEW. Never fabricate evidence, test results, or audit verifications.

---

## 10. Audit Impact Rule

When modifying auth, data models, APIs, permissions, payments, dependencies, or external services, flag the affected audit domain. Do not assume existing audit remains valid after material changes; trigger an audit re-check.
The audit status model (`PASS`, `WARN`, `REVIEW`, `BLOCK`, `NOT_APPLICABLE`, `NOT_VERIFIED`) applies strictly to audit findings. Functional testing statuses (`PASS`, `FAIL`, `BLOCKED`) and Flow Auditor classifications (`BLOCKING`, `FLOW GAP`, `DOCUMENTATION DRIFT`, `IMPROVEMENT`, `PASS`) remain separate and unchanged.
