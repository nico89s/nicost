# Stir Business Rules & Domain Invariants

## Changelog
- **2026-09-28**: Created canonical business rules document under `docs/rules/BUSINESS_RULES.md`.

This document is the authoritative source of truth for financial logic, calculation invariants, and domain rules in Stir. No feature implementation, database query, or refactor may violate these rules.

---

## 1. Financial Precision & Currency Rules (BR-FIN)

- **BR-FIN-001 (Currency & Precision)**:
  - All financial transactions and balances are stored in Indonesian Rupiah (IDR).
  - Amounts are integer values representing whole rupiah. Fractions/cents (*sen*) are not supported.
- **BR-FIN-002 (Display Formatting)**:
  - Every currency amount shown to the user MUST use the Indonesian locale (`id-ID`) with period thousand separators (e.g., `Rp 150.000` or `150.000`).
  - Never display raw unformatted numbers like `150000`.
- **BR-FIN-003 (Input Shorthand Support)**:
  - Input fields and parser must support Indonesian colloquial abbreviations:
    - `rb` / `k` = thousands (e.g. `50rb` or `50k` $\rightarrow$ `50.000`)
    - `jt` / `m` = millions (e.g. `1.5jt` $\rightarrow$ `1.500.000`)

---

## 2. Social Ledger & Split-Bill ("Bayarin Dulu") Rules (BR-SOC)

- **BR-SOC-001 (Virtual Pocket Isolation)**:
  - When a user covers an expense for others (e.g. *"Makan 500k BCA (bayarin 350k)"*):
    1. The full amount (`500k`) is deducted from the source account (`BCA`).
    2. Only the user's portion (`150k`) is recorded as `Expense: Food`.
    3. The covered portion (`350k`) is placed into `Virtual_Pocket: Piutang` (Asset / Receivable).
- **BR-SOC-002 (Zero-Skew Reporting)**:
  - Reimbursements from friends (e.g. friend transfers `100k` to GoPay) MUST NOT be recorded as Income.
  - The repayment increases the destination wallet balance (`GoPay +100k`) and reduces `Virtual_Pocket: Piutang (-100k)`.
  - Monthly Income and Expense totals remain completely unaffected by split-bill settlements.
- **BR-SOC-003 (Atomic Lifecycle Mutation)**:
  - All modifications to social receivables and payables must be routed through `social_ledger_command` or reviewed transactions with tenant row-locking (`users_profile FOR UPDATE`).
- **BR-SOC-004 (Bad Debt Write-Off / "Ikhlasin")**:
  - Receivables unsettled after >60 days can be forgiven via the *Ikhlasin* action.
  - Tapping *Ikhlasin* decrements `Virtual_Pocket: Piutang` and writes the remaining balance to `Expense: Social / Donasi`.

---

## 3. Guilt-Free Balance Reconciliation / "Uang Gaib" (BR-REC)

- **BR-REC-001 (Integrity Preservation)**:
  - When real-world wallet balances differ from the app ledger due to unrecorded micro-expenses (e.g. parking, street snacks), users can sync balance in 1 click.
  - The reconciliation creates a balance adjustment record categorized as *Uang Gaib / Selisih Saldo* rather than altering or deleting past historical transactions.
- **BR-REC-002 (Auditability)**:
  - Adjustments must record timestamp, account ID, previous balance, new balance, and absolute discrepancy amount.

---

## 4. Automated Recurring Deductions & Admin Fees (BR-REC-AUTO)

- **BR-AUTO-001 (Scheduled Execution)**:
  - Recurring rules execute automatically at 00:01 AM WIB on the scheduled date.
- **BR-AUTO-002 (Silent Admin Fee Templates)**:
  - Pre-built templates for bank admin fees (e.g. BCA Rp 15.000 on the 20th) execute without active user confirmation.
  - Deductions are summarized and bundled into the evening WhatsApp Daily Digest recap.

---

## 5. Security & Isolation Invariants (BR-SEC)

- **BR-SEC-001 (Tenant Isolation)**:
  - Every database query must enforce `auth.uid() = user_id`.
  - Cross-user data leakage is a critical severity violation.
- **BR-SEC-002 (No Privilege Elevation in Client)**:
  - Client application must never possess or use Supabase `service_role` keys.
- **BR-SEC-003 (Historical Transaction Preservation)**:
  - When an account is deleted, foreign keys in `transactions` are set to `NULL` (`ON DELETE SET NULL`), preserving historical amounts, reporting figures, and category analytics.

---

## 6. Database Schema & Entity Relationships

The complete entity relationship model is defined in [docs/rules/database-schema.txt](database-schema.txt).
Major entities:
- `USERS`: Tenant identity, preferences, WhatsApp phone, subscription tier.
- `ACCOUNTS`: Real-world wallets and bank accounts.
- `CATEGORIES`: Two-level hierarchical expense/income categories.
- `TRANSACTIONS`: Immutable ledger entries.
- `SPLIT_BILLS` / `VIRTUAL_POCKETS`: Receivables, payables, and peer tracking.
- `RECURRING_RULES`: Cron-executed automated deductions.
