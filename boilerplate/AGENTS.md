# Repository Agent Constitution (AGENTS.md)

## Purpose & Scope

This file defines the mandatory working contract for all AI agents (Antigravity, GitHub Copilot, Claude Code, etc.) operating in this repository.

Project documentation is the single source of truth for product behavior and architecture. Implementation must strictly remain consistent with documented specifications.

---

## 1. Project Sources of Truth & Context Routing

Before executing or proposing non-trivial code modifications, inspect the authoritative documentation:

| Domain | File Path | Focus |
|---|---|---|
| **Product Requirements** | [`docs/PRD.md`](docs/PRD.md) | Problem, goals, target audience, functional scope |
| **UI & Design System** | [`docs/DESIGN_GUIDELINES.md`](docs/DESIGN_GUIDELINES.md) | Component primitives, typography, colors, navigation |
| **System Architecture** | [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Tech stack, module structure, data flows, constraints |
| **User Stories & ACs** | [`docs/user-stories/`](docs/user-stories/) | Gherkin acceptance criteria and test matrices |
| **User Flows & Journeys** | [`docs/flows/`](docs/flows/) | Mermaid flowcharts, reachability contracts, entry/exit |
| **Business Rules** | [`docs/rules/BUSINESS_RULES.md`](docs/rules/BUSINESS_RULES.md) | Domain invariants, calculations, validation rules |
| **Product Changelog** | [`docs/CHANGELOG.md`](docs/CHANGELOG.md) | Product-level changes under `## [Unreleased]` |
| **Developer Preferences** | [`DEVELOPER.md`](DEVELOPER.md) | Universal collaboration habits and style constraints |
| **Procedural Skills** | [`.agents/skills/`](.agents/skills/) | Step-by-step guides for frontend, debugging, testing |
| **Flow Auditor Agent** | [`.github/agents/flow-auditor.md`](.github/agents/flow-auditor.md) | Independent auditor for flow completeness & discovery |

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
- Its component exists;
- Its route or endpoint exists;
- Its automated tests pass;
- It can be opened via a direct URL.

**The Reachability Rule**: Every user-facing screen must have a legitimate, discoverable navigation path from the main app interface.
- Audit all flows: `Discovery → Entry → Navigation → Action → Feedback → Exit/Return`.
- Verify back-button behavior, cancellation actions, and success redirects.
- Run the [`.github/agents/flow-auditor.md`](.github/agents/flow-auditor.md) agent to challenge reachability whenever adding or modifying screens.

---

## 5. UI & Design System Strictness

Implementation must strictly follow [`docs/DESIGN_GUIDELINES.md`](docs/DESIGN_GUIDELINES.md):
- **Reuse Primitives**: Always reuse existing shared components and primitives. Do NOT write one-off custom components when an established component exists.
- **Visual Consistency**: Follow established typography scales, spacing tokens, and semantic colors.
- **Feedback & States**: Every interactive form must handle loading, validation errors, empty states, and success feedback.

---

## 6. Business Rules & Invariants

All domain rules in [`docs/rules/BUSINESS_RULES.md`](docs/rules/BUSINESS_RULES.md) are authoritative:
- Invariants must never be violated for UI convenience.
- Report conflicts rather than silently bypassing a rule.

---

## 7. Change Discipline & Non-Destructive Edits

- Understand existing code and active dependencies before proposing changes.
- Make the smallest coherent, non-destructive diff.
- Do not create parallel or duplicate implementations of existing abstractions.
- Ask for user confirmation before destructive operations, schema drops, or major library additions.

---

## 8. Verification & Evidence Reporting

Never claim a task is complete or that tests passed without executing verification.
- Run project static checks / linters.
- Follow the testing protocol in [`.agents/skills/testing/SKILL.md`](.agents/skills/testing/SKILL.md).
- Report outcomes explicitly as `PASS`, `FAIL`, or `BLOCKED` with supporting evidence.
