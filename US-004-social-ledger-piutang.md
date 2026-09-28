## Changelog
- **2026-09-26**: Utang & Piutang Keyboard Navigation & Unselected Default Category (`app/modal/catat-utang.tsx`, `app/modal/catat-piutang.tsx`, `app/(tabs)/piutang.tsx`): defaulted Catat Utang category to unselected empty state (`Pilih Kategori`) with mandatory validation; fixed top-left corner clipping in Catat Utang category modal by adding `pt-5` clearance and removing redundant header `✕` button; and configured standard `enterKeyHint="next"` / `enterKeyHint="done"` keyboard focus navigation and mobile autofill suppression across all Utang & Piutang forms.
- **2026-09-26**: Clean Debt Detail Header Separator (`components/DebtHistory.tsx`, `app/(tabs)/aktivitas.tsx`): removed redundant separator border lines above *Riwayat transaksi* in transaction detail modals (`DebtHistory`).
- **2026-09-26**: 2-Step Category Selector Modal with Accordion Subcategories & Batal/Pilih Buttons (`app/modal/catat-utang.tsx`, `app/(tabs)/piutang.tsx`): upgraded category pickers in Catat Utang modal and Piutang settlement/ikhlas flows to standardized 2-step category selector modals with 2-level macro/sub-category tree navigation, live category taxonomy connection via `getCategories()`, and standard `Batal` + `Pilih` commit buttons.
- **2026-09-26**: Standardized Date Selection to Tap-Anywhere Rows with Calendar Outline Icon (`app/modal/catat-piutang.tsx`, `app/modal/catat-utang.tsx`, `app/(tabs)/piutang.tsx`): upgraded date pickers from default dropdown styles to full-width tap-anywhere rows showing formatted Indonesian dates (`D MMM YYYY`) with clean outline calendar SVG icons and instant `showPicker()` native triggers.
- **2026-09-08**: Elevated "Catat Piutang" and "Catat Utang" to Universal Actions accessible from any screen via FAB Speed Dial. Standardized both creation forms to Form 1: Slide-Up Bottom Sheet Modals (`/modal/catat-piutang` and `/modal/catat-utang`, `rounded-t-[1.6rem]`), and synchronized `S06` in-page button triggers (`+ Catat Piutang Baru` and `+ Catat Utang Baru`) to open these unified bottom sheets.
- **2026-09-07**: Updated Aktivitas debt settlement presentation ("Terima Piutang" / "Bayar Utang" labels, plain emoji icons, and in-place `<DebtHistory>` modal embedding). Fixed "Koreksi" action in `<DebtHistory>` for origin debts (routing to Quick Log) and repayments (inline edit with atomic review). Enforced `no-scrollbar` across all Piutang & Utang modals and dropdowns.
- **2026-09-07**: Removed `✕` close button from Edit Catatan Piutang and Edit Catatan Utang modal headers in `app/(tabs)/piutang.tsx` to conform to Stir clean header modal standards.
- **2026-09-06**: Accepted social-ledger finalization: partial payments and categorized non-cash write-offs after 30 days; reviewed corrections/reversals preserve audit history and correct original periods. Demo/live parity and isolated verification are required; prior Passed labels are not completion evidence.
- **2026-09-05**: Standardized all modal dialogs in `app/(tabs)/piutang.tsx` to Centered Pop-Up Modal Standard (`bg-ink/40 backdrop-blur-[1px]`), added `createDirectPiutang` with wallet deduction & 0 reportable expense, added dual-mode `createUtang` (cash loan vs covered expense), added smart wallet balance synchronization on edit, and implemented deletion flow with automatic wallet balance restoration.
- **2026-08-31**: Added inline editing for Piutang entries (target amount, debtor name, notes) and implemented complete Utang persistence (`createUtang`, `updateUtang`, `getMyUtangs`) supporting both Demo mode and Live DB (`virtual_pockets` table with type `utang`).

# US-004: Social Ledger (Piutang) & WhatsApp Debt Reminders

| Field | Value |
|---|---|
| **Story ID** | US-004 |
| **Plane Issue Key** | [STIR-9](https://app.plane.so/kickai/projects/f7075e66-202c-41fc-aa0f-45955c3bbddf/issues/bd291b83-3319-4020-a166-7d170001a8ad) |
| **Title** | Social Ledger (Piutang) & WhatsApp Debt Reminders |
| **Module** | Social Ledger & Split Bills (`6b9521b5-2e71-4656-9c8b-6aa3ec667659`) |
| **Screen ID** | S06 (`app/(tabs)/piutang.tsx`), `/modal/catat-piutang`, `/modal/catat-utang` |
| **Priority** | Medium |
| **Plane State** | Done |
| **Verification Status** | Automated Verified |

---

## 👤 User Story
**As a** user who frequently covers restaurant bills, coffee orders, or ride shares for friends,  
**I want to** track who owes me money ("Uangku di orang") with age indicators and generate polite WhatsApp reminder links in one tap,  
**So that** I get paid back on time without feeling awkward or forgetting small debts.

---

## 🎯 Acceptance Criteria

### Scenario 1: Receivables & Payables Summary Overview
- **Given** the user navigates to the **Social Ledger** tab (`S06`),
- **When** the page loads,
- **Then** the UI displays twin summary cards:
  - **Uangku di orang (Piutang)**: Sum of all unpaid active debts owed to the user.
  - **Utangku ke orang (Utang)**: Sum of active debts owed by the user to others.
- **And** formats all currency values with IDR dot thousand separators (`id-ID`).

### Scenario 2: Age-Based Urgency Indicator Badges
- **Given** a list of outstanding receivables in `S06`,
- **When** displaying debt items,
- **Then** the UI applies color-coded age indicators on the left border:
  - **Green (`bg-lime`)**: Debt age $\le 10$ days.
  - **Amber (`bg-amber-warn`)**: Debt age between $11$ and $30$ days.
  - **Red (`bg-danger`)**: Debt age $> 30$ days (Severely overdue).

### Scenario 3: One-Tap WhatsApp Nagging Link Generation
- **Given** an active debt item in the list,
- **When** the user taps **"Tagih via WA"**,
- **Then** the system opens a `wa.me` URL with pre-filled Indonesian nagging text containing:
  - Debtor's name.
  - Item/occasion description (e.g., `"Nongkrong McD"`).
  - Exact amount owed (e.g., `Rp 450.000`).
  - Polite call-to-action to transfer to the user's primary wallet.

### Scenario 4: Settlement ("Terima Bayar") Flow
- **Given** a debtor repays their bill,
- **When** the user taps **"Terima Bayar"** on the debt card,
- **Then** `settleSocialDebt()` executes in `lib/social.ts`:
  1. Deposits the full amount into the user's designated liquid account (e.g. `BCA Tahapan`).
  2. Updates `virtual_pockets` record status to settled (`current_amount = 0`).
- **And** removes the card from the active "Yang belum bayar" feed immediately.

### Scenario 5: Write-Off ("Ikhlaskan") Flow for Overdue Debts
- **Given** a debt item older than 30 days (`age_days > 30`),
- **When** the user taps the **"Ikhlaskan"** button,
- **Then** `writeOffSocialDebt()` executes:
### Scenario 6: Direct Piutang ("Catat Piutang Baru") Creation
- **Given** the user is on the Piutang tab (`viewMode = 'piutang'`),
- **When** the user taps **"+ Catat Piutang Baru"**, enters debtor name, amount, and selects source wallet,
- **Then** `createDirectPiutang()` executes:
  1. Deducts the amount from the selected wallet balance (-Kas).
  2. Creates a receivable in `virtual_pockets` with `type = 'piutang'`.
  3. Records a ledger transaction with `reportable_amount = 0` (Asset transformation, NOT counted in monthly expense).

### Scenario 7: Dual-Mode Utang ("Catat Utang Baru") Creation
- **Given** the user is on the Utang tab (`viewMode = 'utang'`),
- **When** the user taps **"+ Catat Utang Baru"**,
- **Then** the user can choose between:
  - **Pinjam Dana / Tunai**: Money entered wallet (+Kas, +Utang, 0 income). When paid back: -Kas, -Utang, 0 expense.
  - **Ditalangi Belanja / Makan**: No immediate cash movement (+Utang). When paid back: -Kas, -Utang, and logs expense in the chosen category.

### Scenario 8: Smart Wallet Balance Synchronization on Edit
- **Given** a directly recorded Piutang or cash-loan Utang,
- **When** the user edits the nominal amount (e.g. correcting a typo),
- **Then** the system automatically adjusts the difference on the linked source/destination wallet balance.

### Scenario 9: Deletion with Automatic Reversal
- **Given** an existing Piutang or Utang entry,
- **When** the user opens the Edit modal and taps **"Hapus"**,
- **Then** a centered confirmation dialog appears:
  - If linked to a cash loan wallet, deleting it automatically reverts the initial cash impact (refunds piutang cash, or deducts borrowed cash).

---

## 🧪 Verification Matrix

| AC Ref | Scenario | Verification Type | Execution Target / Command | Status |
|---|---|---|---|---|
| **AC-1** | Receivables Total Calculation | Integration Test | `lib/social.ts` (`getSocialDebts`) | 🟢 Logic Verified |
| **AC-2** | Age-Based Color Badges ($\le10$, $11-30$, $>30$) | UI Component Test | Inspect `S06` border colors | 🟢 Passed |
| **AC-3** | WhatsApp Deep Link Generator | Unit Test | `generateWANaggingLink()` in `lib/social.ts` | 🟢 Passed |
| **AC-4** | "Terima Bayar" Settlement Mutation | Integration Test | `settleSocialDebt()` database test | 🟢 Passed |
| **AC-5** | "Ikhlaskan" Write-Off Flow | Integration Test | `writeOffSocialDebt()` check | 🟢 Passed |
| **AC-6** | Direct Piutang (`reportable_amount = 0`) | Unit/Integration | `createDirectPiutang()` | 🟢 Passed |
| **AC-7** | Dual-Mode Utang Lifecycle | Unit/Integration | `createUtang()` & `payUtang()` | 🟢 Passed |
| **AC-8** | Smart Wallet Difference Sync | Unit/Integration | `updateSocialDebt()` & `updateUtang()` | 🟢 Passed |
| **AC-9** | Deletion Reversal | Unit/Integration | `deleteSocialDebt()` & `deleteUtang()` | 🟢 Passed |

---

## 🔗 Code & System Mapping

- **Screen Component**: `app/(tabs)/piutang.tsx` & `components/kit/screens.tsx` (`S06`)
- **Social Debt Engine**: `lib/social.ts`
- **Database Tables**: `virtual_pockets`, `transactions`

## Accepted finalization contract (2026-09-06)

- Direct loans and cash borrowing/repayment do not inflate reports; covered expenses are recognized on repayment.
- Partial payment and write-off amounts must be positive and no greater than outstanding. Write-offs are available after 30 days, use the selected category, and never move cash.
- Corrections and deletion require a preview of every affected transaction, wallet, outstanding balance, and original reporting period, then confirmation against unchanged state.
- Preserve valid repayments on principal edits; if the new principal is below resolved amounts, explicitly preview reversal of related activity before correction.
- Delete a direct debt by reversing origin and related activity. Delete one split receivable by reversing its activity and counting its share as personal spending; preserve the purchase and other friends.
- Deleted wallets receive history-only corrections, with a warning and no current-wallet adjustment. Keep paid, forgiven, and voided history distinguishable.
- WhatsApp is a user-sent prefilled link without hardcoded payment details; this supersedes the original automated-bot requirement. The 30-day availability supersedes the PRD 60-day rule.
- Current status: implementation and verification in progress. Final evidence will be recorded in the social-ledger verification handoff.
