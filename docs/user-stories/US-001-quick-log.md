# US-001: Natural Language & Fast Transaction Quick-Log

## Changelog
- **2026-09-28**: Text-Only Intent Dropdown, Full-Width Category/Notes & 2-Row Compact Textarea (`app/modal/ai-log.tsx`): removed emojis/icons from the *Ganti Jenis* intent dropdown menu in favor of clean text-only options with green checkmark active indicators; repositioned user share summary (`Kamu: Rp ...`) below the *Beban Teman* input in split bill mode; expanded category selector and notes/merchant fields to full width across pengeluaran, pemasukan, and split bill modes; and compacted the natural language prompt textarea to true 2-row height (`h-14`) with streamlined padding and positioned indicators.
- **2026-09-28**: Fully In-Place Editable AI Quick Log Detection Card (`app/modal/ai-log.tsx`, `lib/parser.ts`): transformed the review card into an interactive, all-parameter editable form allowing users to switch intent (Pengeluaran, Pemasukan, Transfer, Split Bill, Utang, Piutang) on the fly via a clickable type pill; adjust nominal amounts via numeric input with live dot thousand formatting; toggle wallets via custom Stir `<BrandLogoIcon />` dropdowns (upward opening on lower rows to prevent clipping); select categories via Stir's standardized 2-Step Category Selector Modal Overlay with macro & subcategory accordion and Batal/Pilih commit buttons; configure payee, social contact, and split bill allocations with auto-recalculated user spend; select custom dates via tap-anywhere rows; and streamlined footer actions to Batal (28%) and Simpan.
- **2026-09-28**: Unified 3s Staged Timing for Example Chips, Slang Parsing (`pinjem`, `minjemin`, `atm`, `setor`) & App Dropdown Styling (`lib/parser.ts`, `app/modal/ai-log.tsx`, `scripts/test-parser.ts`): aligned quick example chips (`makan 30rb cash`, `minjem dodi 50`) to follow the exact same 3-second staged parsing flow (2s idle pause + 1s scanning animation) eliminating double-run artifacts; added support for short slangs (`pinjem andi 100` -> Utang 100k to Andi, `minjemin doni 50` -> Piutang 50k to Doni, `atm 200` -> Cash withdraw 200k to cash, `setor 300 mandiri` -> Cash deposit 300k to Mandiri); replaced native browser select dropdowns in Transfer review card with Stir's signature custom dropdowns featuring `<BrandLogoIcon />`, checkmarks (`✓`), and lime active selections; and integrated `BrandLogoIcon` beside wallet indicators in standard review cards.
- **2026-09-28**: Transfer & Cash Withdraw Parser, 2s+1s Staged Loading Animation & Transfer Wallet Dropdowns (`lib/parser.ts`, `app/modal/ai-log.tsx`, `scripts/test-parser.ts`): added specialized transfer & ATM cash withdrawal parsing (`tarik tunai`, `ambil atm`, `tarik uang` -> source bank to Uang Tunai); separated Transfer UI into single-row Nominal and dual interactive dropdown selectors for *Sumber* (Dari) and *Tujuan* (Ke); configured staged 2-second idle pause followed by 1-second animated scanning badge; streamlined textarea to 2 rows; and simplified quick examples to 2 instant chips (`makan 30rb cash`, `minjem dodi 50`) with zero-latency instant parsing.
- **2026-09-28**: Offline Global Merchant Dictionary Baseline, Smart Text Social Debts (Utang, Piutang, Split Bill), 1000x Multiplier Rule & 3s Debounce UI Polish (`constants/globalDictionary.ts`, `lib/dictionary.ts`, `lib/parser.ts`, `app/modal/ai-log.tsx`, `scripts/test-parser.ts`): built static offline global dictionary baseline containing ~500+ curated Indonesian merchant patterns ($0 token, immutable baseline); upgraded dictionary lookup to local-first cache with non-blocking Supabase sync; extended parser with default <4-digit thousand multiplier rule (`20` -> `20.000`), live user-owned wallet matching with graceful unowned account fallback, and natural language intent parsing for direct utang (`minjem 300rb robi cash`), direct piutang (`pinjemin 50rb roni bca`), and split bill (`makan 100rb bayarin roni 40rb gopay`); and polished AI Text Input modal with 3-second debounce parser, bottom-right animated scanning badge, conditional quick example chips concealment, and rebalanced footer buttons (`Batal` smaller, `Koreksi` & `Simpan` wider with uniform `text-[13px] font-bold` typography).
- **2026-09-27**: Dual-Axis FAB Speed Dial (Horizontal Smart AI Bar & Vertical Stack) & Generic Receipt Vision Quick Log (`components/FabSpeedDial.tsx`, `app/modal/photo-log.tsx`, `lib/statement-skills/profiles/receipt.skill.ts`, `lib/statement-skills/receiptParser.ts`, `lib/walletMatching.ts`, `lib/photoLogDraft.ts`): re-architected FAB Speed Dial into a dual-axis layout pairing a horizontal AI Smart Bar (`[📸 Input foto]` and `[⚡ Input teks]`) in the immediate lower thumb zone with standard upward vertical actions (`Pengeluaran`, `Pemasukan`, `Transfer`, `Utang`, `Piutang`); added native camera/gallery capture file trigger; added Generic Receipt Vision Skill for single receipts, EDC slips, and transfer proofs; and built Form 1 Photo Log scanning modal with animated laser scanline and auto-transition to prefilled Quick Log modal.
- **2026-09-26**: Quick Log UI Polish & Flat Icon Standardization (`components/kit/screens.tsx`): replaced emojis in toggle labels with clean flat outline SVG icons (`HandshakeIcon` for *Bayarin / Split bill* and `RepeatIcon` for *Jadikan Transaksi Rutin*); removed the divider border line above the bottom action buttons; and removed the redundant `✕` close button from the Calculator Modal header.
- **2026-09-08**: Overhauled FAB into animated Speed Dial. Decoupled Quick Log (`S08`) into dedicated single-mode forms (`expense`, `income`, `transfer`), removing internal tab switcher and text input box. Natural language text parsing extracted to dedicated universal action modal `modal/ai-log`.
- **2026-09-07**: Updated Quick Log date selection to use a tap-anywhere non-typeable calendar input box with transparent date picker overlay and instant `showPicker()` invocation, eliminating keyboard caret typing.
- **2026-09-04**: Enhanced Split Bill edit prefill in Quick Log (`S08`) to preserve both user and friend amounts and debtor names upon editing. Removed `"Yang dibeli / Penerima"` input field when `type === "transfer"`.
- **2026-08-31**: Preserved original transaction date on Quick Log edit (`editDraft.occurred_at || editDraft.created_at`) and added "Kapan?" date selector field to Transfer tab layout in `components/kit/screens.tsx`.
- **2026-09-03**: Reordered Quick Log modal layout so Item field (`"Yang dibeli / Penerima"`) appears as Row 2 (above Category selector). Updated Item placeholder to `"Makan, jajan, Rosi OB kantor, dll..."` with consistent font styling. Implemented 5-item floating dictionary autocomplete dropdown for 1-tap item & category selection. Enforced per-user isolation for dictionary entries (prioritizing `user_id = auth.uid()` over global shared defaults) and prevented dictionary self-learning on empty/fallback item entries.
- **2026-09-03**: Defaulted `wallet`, `destWallet`, `categoryMacro`, and `categorySub` to unselected (`""`). Category modal opens in a fully collapsed state with no pre-selected category when unselected. Replaced top warning banner with inline red border field highlights (`border-danger ring-1 ring-danger bg-danger/5`) when attempting to save unselected required fields. Simplified category checkmark badge to `✓`. Integrated dynamic category icon resolution matching `INITIAL_CATEGORIES`. Defaulted initial amount to `0` and fallback empty Item to category name.

| Field | Value |
|---|---|
| **Story ID** | US-001 |
| **Plane Issue Key** | [STIR-6](https://app.plane.so/kickai/projects/f7075e66-202c-41fc-aa0f-45955c3bbddf/issues/581fda4a-198b-4f43-bbc8-d2688aa0f564) |
| **Title** | Natural Language & Fast Transaction Quick-Log |
| **Module** | Add Transaction (`6b2746e4-9f0b-4066-9b67-1bd86703db2e`) |
| **Screen ID** | S08 (`app/modal/quick-log.tsx` & `components/kit/screens.tsx`) & AI Log (`app/modal/ai-log.tsx`) |
| **Priority** | High |
| **Plane State** | Done |
| **Verification Status** | Automated Verified |

---

## 👤 User Story
**As a** busy Indonesian smartphone user managing daily personal finances,  
**I want to** quickly record transactions using an animated Speed Dial menu, dedicated single-mode quick log sheets, or an AI natural language input modal,  
**So that** I can log expense, income, transfer, and debts within 3 seconds without tedious manual dropdown selections or form mode toggling.

---

## 🎯 Acceptance Criteria

### Scenario 1: Dedicated Natural Language AI Text Logging (`modal/ai-log`)
- **Given** the user opens the Speed Dial menu from the Floating Action Button and selects **"Input Teks AI"**,
- **When** the user types an informal Indonesian input string like `"25rb kopi kenangan gopay"` into the input bar (or taps a sample chip),
- **Then** the parser system (`lib/parser.ts`) extracts:
  - Amount: `25000` (IDR)
  - Merchant / Title: `"Kopi Kenangan"`
  - Account / Wallet: `"GoPay"` (Type: `liquid`)
  - Category Macro: `"Food & Drink"`
  - Category Sub: `"Kopi & Jajan"`
- **And** displays an in-modal structured preview card with:
  - 1-tap **"Simpan Langsung"** action committing directly to the ledger.
  - 1-tap **"Koreksi Manual"** pre-filling and opening the full Quick-Log modal.

### Scenario 2: IDR Multiplier Syntax Handling
- **Given** any text input or calculator expression containing IDR numerical shortcuts,
- **When** the input contains shorthand suffixes:
  - `"rb"`, `"k"`, or `"ribu"` (e.g. `50k`, `150rb`, `25ribu`)
  - `"jt"` or `"juta"` (e.g. `1.5jt`, `2.5juta`)
- **Then** `parseIDRAmount()` converts the value into exact numerical integers:
  - `50k` -> `50000`
  - `150rb` -> `150000`
  - `1.5jt` -> `1500000`

### Scenario 3: Upstream Speed Dial Mode Selection & Dedicated Form Header
- **Given** the user taps the center or floating FAB,
- **When** the user selects **"Tambah Keluar"**, **"Tambah Masuk"**, or **"Transfer"** from the Speed Dial,
- **Then** Quick-Log modal (`S08`) opens directly in that specific mode without internal tab switchers or text input clutter,
- **And** displays a clean Form 1 header (`Catat Pengeluaran`, `Catat Pemasukan`, or `Transfer Antar Dompet`) with descriptive subtitle,
- **And** the form dynamically configures:
  - **Pengeluaran (Expense)**: Shows Source Wallet select, Item/Payee name (optional), Category picker, and Split Bill option.
  - **Pemasukan (Income)**: Shows Destination Wallet select, Item/Payer name (optional), and Income Category picker.
  - **Transfer**: Shows Source Wallet select AND Destination Wallet select, date selector, and notes.

### Scenario 4: Inline Split Bill / Bayarin Temen Logging & Edit Prefill
- **Given** the user is logging or editing an expense in Quick-Log modal,
- **When** the user checks or edits the **"Bayarin Temen / Split Bill"** section,
- **Then** input fields display person names and split amounts (e.g., `"Budi Santoso - 300.000"`),
- **And** when editing an existing split bill transaction, automatically resolves and populates the exact debtor name from Piutang records (`lib/social.ts`) and preserves user & friend amounts,
- **And** upon saving, the system creates or updates both:
  1. Main transaction of full bill amount deducted from selected wallet.
  2. Receivable (Piutang) record in `lib/social.ts` linked to the designated friend.

### Scenario 5: Calculator & Quick Add Shortcuts
- **Given** the amount entry field in Quick-Log modal,
- **When** the user taps quick shortcut pills (`+10rb`, `+50rb`, `+100rb`),
- **Then** the nominal value increases by the corresponding amount,
- **And** tapping the calculator icon opens an in-modal numeric calculator that evaluates standard expressions before populating the final amount.

### Scenario 6: Modal Architecture & UX Baseline
- **Given** the Quick-Log modal `S08` is opened from any screen in the app,
- **Then** it strictly complies with the Stir UI Design Rules:
  - Container corner radius: `rounded-t-[1.6rem]`.
  - Max height: `max-h-[90vh]` with dynamic content fitting.
  - Dismiss button: Bottom sticky `Batal` button with `tone="ghost"` (`!w-[28%] shrink-0`).
  - CTA button: Primary action `Simpan Transaksi` (`!w-auto flex-1`, `rounded-2xl`).
  - Money formatting: Formatted with dot thousand separators (e.g. `Rp 25.000`).

### Scenario 7: Optional Item Field & Category Fallback
- **Given** the Quick-Log modal is open,
- **When** the user leaves the Item / Payee field empty (or user selects a main macro category or subcategory),
- **Then** the Item / Payee field is not mandatory,
- **And** upon saving, the system automatically assigns `merchant_name` to the chosen subcategory name (or main macro category name if chosen directly).

### Scenario 8: Item Field Layout, Autocomplete & Clear Button
- **Given** the Quick-Log modal `S08`,
- **When** viewing the input form,
- **Then** the **Yang dibeli / Penerima** field appears as Row 2 (directly above Category selector),
- **And** uses font styling `font-medium` (`text-[14px] text-ink placeholder:text-ink/30 font-sans`),
- **And** typing $\ge 2$ characters displays a floating 5-item deduplicated autocomplete dropdown,
- **And** typing only shows suggestions without auto-preselecting the category until an option is explicitly tapped,
- **And** tapping a far-right `✕` clear button resets text, suggestions, and auto-filled state.

### Scenario 9: Per-User Isolated Dictionary Learning
- **Given** dictionary lookup and transaction saving,
- **When** matching or saving learned items,
- **Then** user private entries (`user_id = auth.uid()`) take precedence over global shared defaults (`is_global = true`),
- **And** user-learned items are saved with `is_global = false` and `user_id = auth.uid()`,
- **And** self-learning is skipped when the Item field is left empty or set to fallback text.

---

## 🧪 Verification Matrix

| AC Ref | Scenario | Verification Type | Execution Target / Command | Status |
|---|---|---|---|---|
| **AC-1** | Text Parsing (25rb Kopi GoPay) | Automated Unit Test | `npx ts-node scripts/test-parser.ts` | 🟢 Passed |
| **AC-2** | IDR Multipliers (50k, 1.5jt) | Automated Unit Test | `lib/parser.ts` (`parseIDRAmount`) | 🟢 Passed |
| **AC-3** | Mode Switching (Expense/Income/Transfer) | Manual / UI Test | Open `S08` -> Tap tabs -> Verify field visibility | ⚪ Untested |
| **AC-4** | Split Bill & Social Ledger Creation | Integration Test | `lib/transactions.ts` + `lib/social.ts` | ⚪ Untested |
| **AC-5** | Keypad Shortcuts & Calculator | Manual / UI Test | Open `S08` -> Tap `+50rb` -> Verify total amount | ⚪ Untested |
| **AC-6** | UI Baseline Compliance | Manual Audit | Check modal styling against `AGENTS.md` spec | 🟡 Manual Verified |

---

## 🔗 Code & System Mapping

- **Modal Component**: `app/modal/quick-log.tsx` & `components/kit/screens.tsx` (`S08`)
- **Parser Engine**: `lib/parser.ts`
- **Merchant Dictionary**: `lib/dictionary.ts` & `supabase/merchant_dictionary.sql`
- **Transaction Mutations**: `lib/transactions.ts`
- **Unit Test Script**: `scripts/test-parser.ts`
