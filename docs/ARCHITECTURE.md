# Architecture & System Overview

## Changelog
- **2026-09-28**: Unified architecture documentation into standardized `docs/ARCHITECTURE.md`, incorporating React Native/Expo cross-platform blueprint, 3-phase development workflow, and `.agents/` structure.
- **2026-09-22**: Documented Financial Reporting & Analytics Suite engine (`lib/reportEngine.ts`), dark-themed analytical charts (`components/reports/*`), Top 5 slice capping with grey "Lainnya", tree hierarchy connectors for subcategories, parent-relative percentage calculations, anchored chart average badges, local timezone date integrity helpers (`formatLocalDateStr`, `getTxLocalDateStr`), and 2-column Aktivitas-style filter bar with 2-tap category verification.
- **2026-09-06**: Documented atomic social ledger lifecycle RPC (`social_ledger_command`), simulation preview tokens, row-level pessimistic tenant locking, `social_ledger_audit` logging, and client review mediator (`lib/ledgerReview.ts` & `components/LedgerReview.tsx`). Updated `lib/transactions.ts` sensitive paths to reflect routing through reviewed social commands.
- **2026-09-05**: Documented atomic account management RPCs (`create_account` with `p_icon`, `update_account`, `delete_account`) and account RLS policies in migration `202609050001_accounts_management_rpcs.sql`. Updated security boundaries to reflect owner-scoped account deletion/update grants and `transactions` foreign key `ON DELETE SET NULL` historical preservation.
- **2026-09-03**: Updated `merchant_dictionary` security & data model documentation for per-user dictionary isolation (`user_id = auth.uid()`), deduplicated lookup queries, and user history priority over global shared defaults.

---

## 1. Stack and Runtimes

- **Core Framework**: React Native with Expo (Managed Workflow, Expo 51, RN 0.74) with strict TypeScript. No bare React Native or custom iOS/Android native code.
- **Routing & Navigation**: `expo-router` (file-based routing). All application screens live under `/app` to ensure identical navigation across web URLs and native mobile view stacks.
- **Backend, Auth & Database**: Supabase (`@supabase/supabase-js`) with PostgreSQL, Row Level Security (RLS), and custom RPCs.
  - `@react-native-async-storage/async-storage` as custom storage adapter for Supabase Auth session persistence across mobile and web.
- **Styling**: NativeWind (Tailwind CSS for React Native) or standard React Native `StyleSheet`. Avoid web-only CSS libraries that crash native mobile view engines.
- **Icons**: `@expo/vector-icons` (Lucide and Ionicons).
- **Showcase / Prototyping Runtime**: Vite 5, React Native Web, PostCSS for desktop browser preview starting at `src/main.tsx`.

Two frontend paths currently coexist:
1. `app/`: Expo Router route shells and screen entry points.
2. `src/main.tsx`: Vite prototype rendering through a mock router. The shared kit in `components/kit/` currently uses DOM primitives for browser showcase while native compatibility is being finalized.

---

## 2. Core Architectural & Coding Guardrails

1. **Universal Primitives Only**:
   - Universal React Native primitives (`<View>`, `<Text>`, `<Image>`, `<TouchableOpacity>`, `<ScrollView>`) must be used for cross-platform components so Expo compiles them cleanly to Android widgets and web DOM elements simultaneously.
   - Do not write raw HTML elements (`<div>`, `<span>`, `<p>`, `<img>`) in shared component files.
2. **Universal SDKs Only**:
   - Features like camera, haptics, secure vault, or push notifications must use official `@expo/...` SDK packages. Avoid libraries requiring manual `pod-install` or custom Android Manifest edits.
3. **Responsive Mobile-First Layouts**:
   - Flexbox with percentage-based and responsive dimensions ensures seamless scaling between vertical mobile screens and desktop web viewports.

---

## 3. The 3-Phase Development Workflow

A lightweight, tool-light workflow without heavy local Android Studio requirements:

- **Phase 1: Rapid UI & Logic Validation (Local Web)**
  - Run `npx expo start --web` (or Vite showcase).
  - Validate layouts, test forms, and verify Supabase queries directly in local desktop browser at `http://localhost:8081`.
- **Phase 2: Native Android Hardware & Feel Testing (Expo Go)**
  - Run `npx expo start` to generate a terminal QR code.
  - Scan using the physical **Expo Go** Android app on a real smartphone over Wi-Fi to test touch gestures, animations, and haptics.
- **Phase 3: Production Android APK/AAB Compilation (EAS Cloud)**
  - Execute `eas build -p android --profile preview` (for direct `.apk` device installs) or `--profile production` (for `.aab` store bundles).
  - Rely on Expo Application Services (EAS) cloud compilation.

---

## 4. Directory Structure

```text
Stir/
├── AGENTS.md                         # Repository Constitution & Agent Working Rules
├── DEVELOPER.md                      # Developer Preferences & Persona Reference
│
├── docs/                             # Documentation Single Source of Truth
│   ├── PRD.md                       # Master Product Requirements Document
│   ├── DESIGN_GUIDELINES.md         # UI/UX Design System, Tokens & Primitives
│   ├── ARCHITECTURE.md              # System Architecture & Development Blueprint (this file)
│   ├── CHANGELOG.md                 # Product-level change history
│   ├── user-stories/                # User stories with Gherkin ACs and test matrices
│   │   ├── README.md                # Story guidelines & index
│   │   ├── US-001-quick-log.md
│   │   ├── US-004-social-ledger-piutang.md
│   │   └── US-014-recurring-transactions.md
│   ├── flows/                       # Visual wireframes and user flow contracts
│   │   └── navigation-wireframe.md
│   └── rules/                       # Business rules, domain invariants, and schemas
│       ├── BUSINESS_RULES.md
│       └── database-schema.txt
│
├── .agents/
│   └── skills/                      # Portable procedural capabilities
│       ├── frontend/SKILL.md        # UI components & styling rules
│       ├── debugging/SKILL.md       # Root-cause analysis & diagnostic commands
│       ├── testing/SKILL.md         # Story verification & test reporting
│       └── accounting-integrity/SKILL.md # Financial invariants & balance reconciliation
│
├── .github/
│   └── agents/
│       └── flow-auditor.md          # Independent user flow & reachability auditor
│
├── app/                             # Expo Router route shells and screen entry points
├── components/                      # Modular cross-screen components (LedgerReview, DebtHistory)
│   └── kit/                         # 16-screen UI compositions and primitives
├── lib/                             # Core engines (parser, dictionary, socialEngine, reportEngine)
├── supabase/                        # Active SQL schemas, RPCs, and ordered migrations
├── reference-kit/                   # Protected visual references (read-only baseline)
└── scripts/                         # Ad-hoc engine/database assertions
```

---

## 5. Active Data Model & Migrations

`supabase/schema.sql` defines profiles, accounts, virtual pockets, transactions, budgets, and recurring rules. `supabase/merchant_dictionary.sql` defines the merchant dictionary.

Ordered incremental migrations are tracked in `supabase/migrations/` and deployed via `supabase db push`. Migration `202609060001_social_ledger_lifecycle.sql` establishes:
- `virtual_pockets.debt_kind`: `'direct_receivable'`, `'split_bill'`, `'cash_payable'`, `'covered_payable'`, `'legacy_review'`
- `virtual_pockets.occurred_at`, `version`, `voided_at`
- `transactions.voided_at`
- Expanded `transactions.event_kind` check: `'receivable_settlement'`, `'receivable_writeoff'`, `'direct_receivable'`, `'cash_borrowing'`, `'payable_settlement'`
- `public.social_ledger_audit` table with user-isolated RLS.
- Atomic RPC `social_ledger_command(p_input JSONB, p_preview BOOLEAN, p_token TEXT)` with per-tenant pessimistic serialization (`users_profile FOR UPDATE`), rollback simulation for previews, cryptographic state token checks, and atomic commit.

For entity relationships and schema diagrams, see [docs/rules/database-schema.txt](rules/database-schema.txt).

---

## 6. Security Boundaries

- **Row Level Security (RLS)**: Enabled across all user-facing tables. Ownership strictly checked with `auth.uid() = user_id`.
- **Restricted Mutations**: Direct `INSERT`, `UPDATE`, and `DELETE` on `public.virtual_pockets` are revoked from `authenticated` and `anon`. All social mutations (direct lending, borrowing, payments, write-offs, corrections, deletions) must pass through `social_ledger_command` or `create_ledger_transaction`.
- **Legacy RPC Protection**: Legacy mutation RPCs (`settle_receivable`, `write_off_receivable`, `update_ledger_transaction`, etc.) have execution revoked to prevent bypassing review and concurrency invariants.
- **Account Isolation & Historical Preservation**: `public.accounts` enforces owner isolation with `accounts_select_own`, `accounts_delete_own`, and `accounts_update_own` policies. Atomic account mutations are executed through `SECURITY DEFINER` RPCs (`create_account`, `update_account`, `delete_account`). Foreign keys on `transactions` use `ON DELETE SET NULL` (`account_id`, `destination_account_id`), ensuring account deletion preserves historical transaction records, spending amounts, and category analytics.
- **Privileged Functions**: `handle_new_user()` runs as `SECURITY DEFINER`; changes require explicit `search_path`, ownership, and caller review.
- **Credential Safety**: Client code may contain the Supabase anon key, but must never receive a service-role key or any privileged credential.

---

## 7. Sensitive Paths

- `lib/socialEngine.ts` and `supabase/migrations/202609060001_social_ledger_lifecycle.sql`: deterministic lifecycle simulation, atomic RPC mutation, row locking, and financial invariance.
- `lib/transactions.ts`: transaction normalization, split bill creation, Safe to Spend, and routing of `updateTransaction` and `deleteTransaction` through reviewed social commands.
- `lib/ledgerReview.ts` & `components/LedgerReview.tsx`: modal review interceptor displaying wallet, transaction, and debt impact previews before committing mutations.
- `lib/reconciliation.ts`: balance adjustment flagging and exact balance update.
- `lib/parser.ts` and `lib/dictionary.ts`: localized amount parsing and dictionary learning.
- `lib/supabase.ts`: client configuration and auth persistence.
