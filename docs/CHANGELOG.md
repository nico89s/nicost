# Product Changelog

All notable changes to the Stir product, specifications, and architecture are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).
Only meaningful changes to product behavior, requirements, architecture, or user flows are recorded here. Internal refactoring, formatting, and trivial bug fixes that do not alter documented behavior are omitted.

---

## [Unreleased]

### Added
- **Framework & Standards Upgrade**: Implemented universal two-tier architecture:
  - Universal repository constitution in `/AGENTS.md`.
  - Structured `/docs/` documentation hierarchy (`PRD.md`, `DESIGN_GUIDELINES.md`, `ARCHITECTURE.md`, `CHANGELOG.md`, `user-stories/`, `flows/`, `rules/`).
  - Standardized `.agents/skills/` suite (`frontend`, `debugging`, `testing`, `accounting-integrity`).
  - Independent Flow Auditor agent in `.github/agents/flow-auditor.md`.

---

## [2026-09-22]

### Added
- **Financial Reporting Suite (Rapor)**: Dark-themed analytical charts (`components/reports/*`), interactive `BarTrendChart`, `CategoryPieReport` with Top 5 capping and "Lainnya" bucket.
- **Category Tree Hierarchy**: Subcategory connectors and parent-relative percentage calculations.
- **Date Integrity Helpers**: Local timezone formatting utilities (`formatLocalDateStr`, `getTxLocalDateStr`).

---

## [2026-09-16]

### Added
- **User Story Parity**: Full bidirectional synchronization of 12 User Stories (`US-001` through `US-017`) with Plane.so issue tracker (`kickai` workspace).

---

## [2026-09-06]

### Added
- **Atomic Social Ledger RPC**: Implemented `social_ledger_command` with pessimistic tenant locking, cryptographic preview tokens, and user audit logging (`public.social_ledger_audit`).
- **Modal Review Mediator**: Interactive pre-commit review interceptor (`lib/ledgerReview.ts`, `components/LedgerReview.tsx`).

---

## [2026-09-05]

### Changed
- **Account Management**: Owner-scoped atomic account management RPCs (`create_account`, `update_account`, `delete_account`).
- **Historical Preservation**: Applied `ON DELETE SET NULL` on `transactions` foreign keys to preserve historical spending totals when accounts are removed.

---

## [2026-09-03]

### Added
- **Merchant Dictionary Isolation**: Per-user dictionary isolation (`user_id = auth.uid()`) with user history lookup precedence over global defaults.
