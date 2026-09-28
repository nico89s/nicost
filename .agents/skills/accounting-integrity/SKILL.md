---
name: accounting-integrity
description: Validate and enforce financial calculation correctness, balance reconciliation, zero-drift invariants, and social split-bill accounting rules in Stir.
---

# Accounting Integrity Skill — Stir Financial Rules

## Objective
Protect financial accuracy and ledger integrity across Stir. Financial invariants must never be bypassed for UI convenience.

---

## 1. Authoritative Reference
Always review [`docs/rules/BUSINESS_RULES.md`](../../../docs/rules/BUSINESS_RULES.md) before altering code in:
- `lib/socialEngine.ts`
- `lib/transactions.ts`
- `lib/reconciliation.ts`
- `lib/reportEngine.ts`
- `supabase/migrations/`

---

## 2. Invariant Checklist

For any feature or modification touching financial data:

1. **Integer Rupiah Invariant**:
   - Amounts must be integers. No fractional cents or floating-point decimals.
   - Assert: `Number.isInteger(amount) && amount >= 0` (or appropriate signed value).
2. **Social Ledger Split-Bill Invariant**:
   - Outflow: Total payment = User Expense + Virtual Piutang.
   - Reimbursement: Friend repayment reduces Piutang and increases Wallet. **Never add to Income.**
   - Bad Debt: *Ikhlasin* reduces Piutang and writes to Expense.
3. **Reconciliation / "Uang Gaib" Invariant**:
   - Discrepancies between physical money and recorded wallet balance must be adjusted via a dedicated adjustment transaction (`event_kind = 'reconciliation'`).
   - Past transaction amounts or timestamps must NEVER be retroactively edited to force balances to match.
4. **Account Deletion Invariant**:
   - Foreign keys on `transactions.account_id` and `transactions.destination_account_id` must use `ON DELETE SET NULL`.
   - Deleting an account must NEVER delete or invalidate historical transactions.
5. **Double-Entry Balance Verification**:
   - Transfers between accounts: Source account decreases by $X$, destination account increases by $X$.
   - Net wealth impact must equal $0$ (excluding transaction/admin fees).

---

## 3. Review Mediator Interception

- All mutations affecting social debts or account balances must route through `lib/ledgerReview.ts` / `<LedgerReview />` modal or `social_ledger_command` RPC.
- Ensure the user sees the pre-commit simulation breakdown (Wallet impact, Debt impact, and Transaction record) before data is committed to PostgreSQL.
