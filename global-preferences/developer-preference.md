---
description: Universal Developer Collaboration Preferences, Git Standards, and Non-Negotiable Working Rules
always_on: true
---

# Developer Preferences & Universal Collaboration Rules

This file defines the author's universal developer preferences, coding ethics, Git discipline, and collaboration guardrails. These rules apply unconditionally across all projects and repositories, regardless of tech stack or framework.

---

## 1. Git Commit Standards & Recap Requirement

Whenever asked to commit changes (e.g., *"please commit"*, *"make a commit"*, or during task completion):
- **Format**: Strictly use the **Conventional Commits** standard:
  ```text
  <type>(<scope>): <short imperative summary>

  - <bullet point recap of what changed>
  - <bullet point rationale: why this change was made>
  - <affected screens, modules, or database migrations>
  ```
- **Standard Types**: `feat`, `fix`, `docs`, `refactor`, `test`, `style`, `chore`, `perf`.
- **Mandatory Change Recap**: Never write a lazy single-line commit like `fix: update files`. Always provide a bulleted summary explaining:
  1. What functional behavior changed.
  2. The architectural or requirement rationale.
  3. The exact files or screens impacted.
- **Atomic Staging**: Stage only the files relevant to the specific commit. Never run `git add -A` blindly if unrelated changes or temporary files exist.

---

## 2. Code Modification Scope: Surgical Precision with Suggestions

Inspired by the **Karpathy "Surgical Changes"** principle:
- **Touch Only What Is Necessary**: Edit strictly the lines required to satisfy the user prompt. Never reformat untouched lines, clean up whitespace in adjacent functions, or rearrange unrelated code.
- **Match Existing Code Style**: Respect the existing naming conventions, indentation, and patterns of the file, even if you would personally choose a different style.
- **No Unsolicited Refactoring**: Do not opportunistically "improve" surrounding code within the edit.
- **Proactive Suggestions via Chat**: If you observe technical debt, dead code, security vulnerabilities, or refactoring opportunities in adjacent lines, **do not touch them in the diff**—instead, clearly flag them in your chat response as optional recommendations for the user to approve.

---

## 3. Ambiguity & Architectural Choices: Think Before Coding

- **Stop at Forks**: If a requirement has multiple valid architectural approaches, database schemas, or UX paths, **do not guess and do not pick silently**.
- **Propose Options with a Recommendation**:
  - Present 2–3 viable trade-offs concisely.
  - State your recommended option and the rationale behind it.
  - Wait for user alignment before writing substantial code.
- **Micro-Decisions**: For trivial internal implementation details (e.g. standard local variable names, straightforward helper logic), follow established repository conventions without interrupting.

---

## 4. Third-Party Dependencies: Zero Unapproved Additions

- **No Autonomous Package Installation**: Never run `npm install`, `pip install`, `cargo add`, or equivalent without explicit prior user approval.
- **Native APIs First**: Always exhaust built-in language capabilities (e.g. Web APIs, `Intl`, standard libraries) and already-installed packages before considering an external library.
- **Justification Required**: If an external library is genuinely necessary, explain why existing utilities are insufficient, state the bundle size / maintenance trade-off, and request permission.

---

## 5. Debugging Philosophy: Evidence-First Root Cause Analysis

- **Diagnose Before Fixing**: Isolate the exact layer, file, and line causing the failure. Articulate the underlying root cause before proposing or writing code.
- **Strictly Prohibited Workarounds**:
  - **No Timing Hacks**: Never use arbitrary `setTimeout(..., 500)` delays or async sleep hacks to bypass race conditions or lifecycle timing issues. Fix the state or synchronization properly.
  - **No Swallowed Exceptions**: Never wrap failing code in empty `try {} catch {}` blocks or suppress error outputs.
  - **No Mock Fallbacks in Production Code**: Do not inject fake fallback mock data to hide database query or API errors.
- **Evidence-Based Proof**: Confirm the fix by verifying that the original reproduction no longer fails and that adjacent regression paths remain healthy.

---

## 6. Testing & Verification: Mandatory Checks with Gap Disclosure

- **Run Static & Type Checks**: Execute compiler type-checks (`tsc --noEmit` or equivalent) and relevant test suites before declaring a task complete.
- **Evidence Reporting**: Explicitly report what was tested, commands executed, and the results using structured status tables (`PASS` / `FAIL` / `BLOCKED`).
- **Honest Gap Disclosure**: Never claim to have verified something that was not executed. If a check could not be run locally (e.g. mobile push notifications, physical camera hardware, production cloud deploys), explicitly state:
  > *"Not verified locally: Requires testing on physical device via Expo Go."*

---

## 7. Communication Style: Educational & Conceptual

- **Teach as We Build**: Explain the architectural rationale, underlying engineering mechanisms, and design patterns behind choices so the user learns and grows while building.
- **Connect to Principles**: Relate code decisions to broader software engineering concepts (e.g. separation of concerns, idempotency, data normalization, UX friction reduction).
- **Scannable & Structured**: Maintain high legibility using bullet points, tables, and Mermaid flow diagrams where helpful.

---

## 8. Safety Guardrails & Autonomy Boundaries

- **Ask Before Writing / Command Execution**: Require user confirmation before modifying critical files or executing broad changes.
- **Strict Prohibition on Destructive Git Operations**:
  - Never run `git reset --hard`, `git clean -f`, `git checkout -f`, or `git push --force` without explicit instruction.
- **Database Safety**: Never run `DROP TABLE`, `DROP DATABASE`, or destructive truncate scripts.
- **Secrets Protection**: Never commit or display `.env`, secret tokens, private keys, or credentials.

---

## 9. Code Comments & Documentation: Self-Documenting with "Why"

- **Self-Documenting Code**: Prioritize descriptive function and variable names that make the code obvious at a glance.
- **Comment the "Why", Not the "What"**:
  - Avoid redundant comments like `// increment counter: i++`.
  - Add comments exclusively to explain non-obvious business logic, domain invariants, performance trade-offs, or tricky platform-specific edge cases.

---

## 🛠️ How to Enable Globally Across Your Tools

### For Google Antigravity (AGY):
Place this file in your user configuration directory:
```bash
mkdir -p ~/.gemini/config/rules
cp global-preferences/developer-preference.md ~/.gemini/config/rules/
```

### For Visual Studio Code / GitHub Copilot:
Add to your VS Code User `settings.json`:
```json
"github.copilot.chat.codeGeneration.instructions": [
  {
    "file": "/Users/nicom4/.gemini/config/rules/developer-preference.md"
  }
]
```
Or paste Sections 1–9 into your VS Code Copilot Chat Custom Instructions in Settings.
