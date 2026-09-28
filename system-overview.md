# System Overview

## Changelog
- **2026-09-22**: Documented Financial Reporting & Analytics Suite engine (`lib/reportEngine.ts`), dark-themed analytical charts (`components/reports/*`), Top 5 slice capping with grey "Lainnya", tree hierarchy connectors for subcategories, parent-relative percentage calculations, anchored chart average badges, local timezone date integrity helpers (`formatLocalDateStr`, `getTxLocalDateStr`), and 2-column Aktivitas-style filter bar with 2-tap category verification.
- **2026-09-06**: Documented atomic social ledger lifecycle RPC (`social_ledger_command`), simulation preview tokens, row-level pessimistic tenant locking, `social_ledger_audit` logging, and client review mediator (`lib/ledgerReview.ts` & `components/LedgerReview.tsx`). Updated `lib/transactions.ts` sensitive paths to reflect routing through reviewed social commands.
- **2026-09-05**: Documented atomic account management RPCs (`create_account` with `p_icon`, `update_account`, `delete_account`) and account RLS policies in migration `202609050001_accounts_management_rpcs.sql`. Updated security boundaries to reflect owner-scoped account deletion/update grants and `transactions` foreign key `ON DELETE SET NULL` historical preservation.
- **2026-09-03**: Updated `merchant_dictionary` security & data model documentation for per-user dictionary isolation (`user_id = auth.uid()`), deduplicated lookup queries, and user history priority over global shared defaults.

## Stack and runtimes

- TypeScript with strict checking
- React Native 0.74 and Expo 51 with Expo Router
- Vite 5, React Native Web, Tailwind CSS, and PostCSS for the current showcase
- Supabase JavaScript SDK with PostgreSQL, Auth, and RLS

Two frontend paths currently coexist. Expo Router defines application routes under `app/`; Vite starts at `src/main.tsx` and renders the showcase through a mock router. The shared kit in `components/kit/` currently uses browser DOM primitives, so the Vite path works while native compatibility remains incomplete.

## Important directories

- `app/`: route shells and screen entry points
- `components/kit/`: frozen-shape sixteen-screen UI compositions and primitives
- `components/`: modular cross-screen components (`LedgerReview.tsx`, `DebtHistory.tsx`)
- `lib/`: parser, dictionary, ledger, social-debt engine (`socialEngine.ts`, `social.ts`), review mediator (`ledgerReview.ts`), reconciliation, and Supabase helpers
- `supabase/`: active SQL definitions and migrations
- `docs/prd/`: historical PRD/schema intent
- `reference-kit/`: protected visual references
- `scripts/`: isolated engine/adapter verification and database assertions

## Active data model

`supabase/schema.sql` defines profiles, accounts, virtual pockets, transactions, budgets, and recurring rules. `supabase/merchant_dictionary.sql` separately defines the shared/private merchant dictionary. This flattened model supersedes the more normalized schema proposed in `docs/prd/01_database-schema.sql` for current implementation work.

Ordered incremental migrations are tracked in `supabase/migrations/` and pushed to the linked cloud database via `supabase db push`. Migration `202609060001_social_ledger_lifecycle.sql` establishes:
- `virtual_pockets.debt_kind`: `'direct_receivable'`, `'split_bill'`, `'cash_payable'`, `'covered_payable'`, `'legacy_review'`
- `virtual_pockets.occurred_at`, `version`, `voided_at`
- `transactions.voided_at`
- Expanded `transactions.event_kind` check: `'receivable_settlement'`, `'receivable_writeoff'`, `'direct_receivable'`, `'cash_borrowing'`, `'payable_settlement'`
- `public.social_ledger_audit` table with user-isolated RLS.
- Atomic RPC `social_ledger_command(p_input JSONB, p_preview BOOLEAN, p_token TEXT)` with per-tenant pessimistic serialization (`users_profile FOR UPDATE`), rollback simulation for previews, cryptographic state token checks, and atomic commit.

## Security boundaries

- User-facing tables have RLS enabled in the active SQL.
- Ownership is strictly checked with `auth.uid()` against the row owner.
- Direct `INSERT`, `UPDATE`, and `DELETE` on `public.virtual_pockets` are revoked from `authenticated` and `anon`. All social mutations (direct lending, borrowing, payments, write-offs, corrections, deletions) must pass through `social_ledger_command` or `create_ledger_transaction`.
- Legacy mutation RPCs (`settle_receivable`, `write_off_receivable`, `update_ledger_transaction`, etc.) have execution revoked to prevent bypassing review and concurrency invariants.
- `public.accounts` enforces owner isolation with `accounts_select_own`, `accounts_delete_own`, and `accounts_update_own` policies. Atomic account mutations are executed through `SECURITY DEFINER` RPCs (`create_account`, `update_account`, `delete_account`), with direct `DELETE` and `UPDATE (name, icon, balance)` granted to `authenticated` users on owned rows.
- Foreign keys on `transactions` use `ON DELETE SET NULL` (`account_id`, `destination_account_id`), ensuring that deleting an account leaves historical transaction records, spending amounts, and category analytics completely intact.
- `handle_new_user()` runs as `SECURITY DEFINER`; changes to privileged functions require explicit `search_path`, ownership, and caller review.
- Client code may contain a Supabase anon key, but must never receive a service-role key or another privileged credential.

## Sensitive paths

- `lib/socialEngine.ts` and `supabase/migrations/202609060001_social_ledger_lifecycle.sql`: deterministic lifecycle simulation, atomic RPC mutation, row locking, and financial invariance
- `lib/transactions.ts`: transaction normalization, split bill creation, Safe to Spend, and routing of `updateTransaction` and `deleteTransaction` through reviewed social commands
- `lib/ledgerReview.ts` & `components/LedgerReview.tsx`: modal review interceptor displaying wallet, transaction, and debt impact previews before committing mutations
- `lib/reconciliation.ts`: balance adjustment flagging and exact balance update
- `lib/parser.ts` and `lib/dictionary.ts`: localized amount parsing and dictionary learning
- `lib/supabase.ts`: client configuration and auth persistence

