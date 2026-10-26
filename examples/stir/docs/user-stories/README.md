## Changelog
- **2026-09-22**: Documented Rapor Financial Reporting Suite and design patterns (`docs/design/ui-design-rules.md`, `docs/development/current-state.md`, `docs/architecture/system-overview.md`) covering interactive BarTrendChart, CategoryPieReport with Top 5 capping and tree hierarchy connector lines, parent-relative subcategory percentages, anchored chart average badges, local timezone date integrity helpers, and 2-column Aktivitas-matching filter bars.
- **2026-09-16**: Synchronized all 12 User Stories (`US-001` through `US-017`) with Plane.so workspace (`kickai`), creating missing issues (`STIR-25` through `STIR-29`), linking module associations, updating Gherkin acceptance criteria descriptions, and recording 100% bidirectional parity in documentation indices.

# Stir User Stories & Acceptance Criteria

This directory serves as the **Single Source of Truth** for user requirements, acceptance criteria (ACs), and test specifications in Stir. All user stories created here are formatted for human readability, AI test execution, and seamless synchronization with **Plane.so**.

---

## 📋 User Story Document Standard

Every user story document (`US-XXX-name.md`) must follow this standard structure:

### 1. Metadata Block
```markdown
# US-XXX: [Title]

| Field | Value |
|---|---|
| **Story ID** | US-XXX |
| **Title** | [Short descriptive title] |
| **Module** | [Transactions / Social / Governance / Analytics / Onboarding] |
| **Screen ID** | [e.g. S08 - app/modal/quick-log.tsx] |
| **Priority** | [High / Medium / Low] |
| **Plane State** | [Backlog / In Progress / In Review / Done] |
| **Verification Status** | [Untested / Manual Verified / Automated Verified] |
```

---

### 2. User Story Statement
```markdown
## 👤 User Story
**As a** [user persona, e.g. young Indonesian professional tracking daily expenses],
**I want to** [action, e.g. record a transaction using natural language or quick keypad],
**So that** [benefit, e.g. I can maintain accurate ledger records without typing friction].
```

---

### 3. Acceptance Criteria (Gherkin Format)
Acceptance criteria must use formal **Given-When-Then** syntax:

```markdown
## 🎯 Acceptance Criteria

### Scenario 1: [Short Scenario Description]
- **Given** [initial state or preconditions]
- **When** [user performs action]
- **Then** [expected result / system response]
- **And** [additional assertions]
```

---

### 4. Test & Verification Matrix
Used by auditors and automated test scripts to verify the story before marking complete in Plane:

```markdown
## 🧪 Verification Matrix

| AC Ref | Verification Type | Test Strategy / Command | Status |
|---|---|---|---|
| AC-1 | Automated (Unit) | `npx ts-node scripts/test-parser.ts` | 🟢 Passed |
| AC-2 | Manual (UI) | Click '+' FAB on S04 -> Modal slides up | 🟡 Manual Verified |
| AC-3 | Automated (E2E) | Component check `S08` form submission | ⚪ Untested |
```

---

## 📂 Index of User Stories

| ID | Title | Plane Key | Plane Module | Scope / Screen | Priority | Status |
|---|---|---|---|---|---|---|
| [US-001](./US-001-quick-log.md) | Natural Language & Fast Transaction Quick-Log | `STIR-6` | Add Transaction | S08 (`app/modal/quick-log.tsx` & `components/kit/screens.tsx`) & AI Log (`app/modal/ai-log.tsx`) | High | Synced to Plane |
| [US-002](./US-002-beranda-dashboard.md) | Beranda Dashboard & Financial Health Summary | `STIR-7` | Dashboard, Rapor & AI Insights | S04 (`app/(tabs)/index.tsx` & `components/kit/screens.tsx`) | High | Synced to Plane |
| [US-003](./US-003-uang-gaib-reconciliation.md) | Uang Gaib Discrepancy & Cash Reconciliation | `STIR-8` | Activity, Cash Burn & Reconciliation | S10 (`app/modal/uang-gaib.tsx` & `components/kit/screens.tsx`) | Medium | Synced to Plane |
| [US-004](./US-004-social-ledger-piutang.md) | Social Ledger (Piutang) & WhatsApp Debt Reminders | `STIR-9` | Social Ledger & Split Bills | S06 (`app/(tabs)/piutang.tsx`), `/modal/catat-piutang`, `/modal/catat-utang` | Medium | Synced to Plane |
| [US-005](./US-005-core-ledger-invariants.md) | Core Ledger & Multi-Account Balance Integrity | `STIR-10` | Core Accounting Engine & Invariants | Cross-cutting (App-wide state & DB mutations) | Urgent | Synced to Plane |
| [US-006](./US-006-transaction-lifecycle-sync.md) | Transaction Lifecycle Mutation & System-Wide Sync | `STIR-11` | Core Accounting Engine & Invariants | Cross-cutting (S04 Dashboard, S05 Aktivitas, S07 Rapor, S13 Budgets) | Urgent | Synced to Plane |
| [US-007](./US-007-split-bill-accounting.md) | Split-Bill & Receivable Accounting Separation | `STIR-12` | Social Ledger & Split Bills | S08 Quick-Log Modal & S06 Social Ledger (`app/(tabs)/piutang.tsx`) | High | Synced to Plane |
| [US-008](./US-008-cash-burn-catchup.md) | Physical Cash Burn-Down & Catch-Up Batch Ingestion | `STIR-25` | Activity, Cash Burn & Reconciliation | S11 (`app/modal/tunai-burn.tsx`) & S09 (`app/modal/catch-up.tsx`) | Medium | Synced to Plane |
| [US-009](./US-009-budget-governance-caps.md) | Flexible Budget Governance & Threshold Enforcement | `STIR-26` | Governance & Budget Hub | S13 (`app/governance/budgets.tsx`) & S07 (`app/(tabs)/rapor.tsx`) | High | Synced to Plane |
| [US-010](./US-010-wallets-paylater-lifecycle.md) | Multi-Wallet Governance & PayLater Debt Lifecycle | `STIR-27` | Governance & Wallets | S12A (`app/governance/wallets.tsx`) & S12B (`app/governance/cicilan.tsx`) | High | Synced to Plane |
| [US-014](./US-014-recurring-transactions.md) | Recurring Transactions & Subscription Hub | `STIR-28` | Core Accounting Engine & Invariants | S14 (`app/governance/recurring.tsx` & `lib/recurring.ts`) | High | Synced to Plane |
| [US-017](./US-017-category-management.md) | Category Management (Kelola Kategori) | `STIR-29` | Governance Hubs | S12D (`app/governance/categories.tsx` & `lib/categories.ts`) | High | Synced to Plane |

