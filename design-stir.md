# Stir UI & Design System Guidelines

This document serves as the single source of truth for UI, styling, component mechanics, and design system patterns in Stir. Every new screen, modal, dropdown, or feedback component MUST follow the specifications defined here to guarantee visual uniformity, predictable UX, and swift implementation.

---

## 1. Typography & Font System

- **Font Family (Modals & General UI)**: Use **Plus Jakarta Sans** (`font-sans`) exclusively across application screens, modals, headers, inputs, labels, and subtext. Do not use monospace (`font-mono`) for general UI labels.
- **Minimum Font Size Floor**: **`11px` (`text-[11px]`)** is the absolute minimum allowed font size anywhere in the app. Micro font sizes (`8px`, `9px`, `10px` for content text) must never be used for content readability.
- **Main Input & Body Text**: Primary values, item names, and input text use **`text-[14px] font-bold text-ink`**.
- **Field Labels & Subtext**: Section headers, field titles, and auxiliary metadata use **`text-[11px] font-bold text-ink/50`** (or `text-ink/45`).
- **Hero Numbers**: Main account balances, neraca figures, and hero amounts use **`text-[24px]` to `text-[36px] font-black tracking-tight text-ink`**.

---

## 2. Number & Financial Formatting

- **Mandatory Dot Thousand Separators**: Every monetary amount or currency value MUST be formatted using Indonesian locale standard (`id-ID`) with dot thousand separators.
- **Format**: `Rp 100.000` or `100.000` (e.g. `amount.toLocaleString('id-ID')` or `formatIDR(amount)`).
- **Rule**: Never display raw, unformatted numbers such as `100000`.

---

## 3. Centralized Kit Primitives (`components/kit/primitives.tsx`)

Always reuse primitives from `components/kit/primitives.tsx` (`<Btn>`, `<Card>`, `<Banner>`, `<Sheet>`, `<Pill>`, `<Label>`, `<Title>`, `<Sub>`). Never write raw `<button>` elements or custom `<div onClick>` containers with arbitrary rounded styles.

### Button Rounding & Proportions
- **Main CTA Buttons (`Btn`)**: Must strictly use `rounded-2xl` (16px border-radius). Do not apply `rounded-full` to narrow action buttons as it squishes them into awkward ovals.
- **Selection Chips & Tab Containers**: Filter pills, category chips, and top segment tabs must use `rounded-full` (`flex rounded-full bg-surface-2 p-1`).
- **Multi-Button Rows (Side-by-Side Footers)**:
  - Container: `flex w-full gap-2 pt-3 pb-1 border-t border-ink/10`
  - Secondary Action (Dismiss/Cancel): `<Btn tone="ghost" className="!w-[28%] shrink-0">Batal</Btn>`
  - Primary Action (Save/Confirm): `<Btn tone="primary" className="!w-auto flex-1">Simpan</Btn>`
  - Destructive Action: `<Btn tone="danger" className="!w-auto flex-1">Hapus</Btn>`
  - This explicit flex sizing guarantees buttons fill 100% of container width without shrinking, wrapping, or overflowing on narrow viewports.

---

## 3B. Notice & Insight Banner System (`Banner`)

Used for **in-flow notices, AI insights, system disclaimers, and motivational target banners** (e.g. Roast Banner on Home, Sherlock Discrepancy on Aktivitas, Freedom Target on Cicilan, Admin Fee reminder on Recurring).

#### Visual Tokens & Anatomy (Baseline Reference: Home & Aktivitas):
- **Container**: `flex items-center gap-3 rounded-2xl p-3 text-ink`
- **Tones**:
  - `tone="lime"`: `bg-lime/20 text-ink` (AI insights, smart roasts, positive goals)
  - `tone="warn"`: `bg-amber-warn/20 text-ink` (Disclaimers, discrepancy warnings, privacy notes)
  - `tone="mint"`: `bg-mint/25 text-ink` (Financial commitments, safe-to-spend tips)
  - `tone="danger"`: `bg-danger/15 text-ink` (Critical system alerts)
  - `tone="plain"`: `bg-surface-2 text-ink` (Neutral info notes)
- **Leading Icon**: `shrink-0 text-[20px] leading-none`
- **Typography**: Single unified body style — **`text-[14px] font-bold leading-snug`** (do not use small 11px/12px text or non-bold weights for top-level banner messages).
- **Interactive State**: When `onClick` is provided: `cursor-pointer transition-opacity hover:opacity-90 active:scale-[0.99]`.

```tsx
import { Banner } from '@/components/kit/primitives';

<Banner icon="🔒" tone="warn">
  Kami tidak berafiliasi dengan pinjol. Data cicilanmu tersimpan lokal di perangkat.
</Banner>
```

---

## 3C. Tip Card System (`TipCard`)

Used for **in-flow contextual micro-guidance, onboarding hints, and explanatory help cards** directly embedded within sheets, modals, or page containers (e.g. Catat Cepat helper card on `[S11]` Tunai Burn-down).

#### Visual Tokens & Anatomy:
- **Container**: `relative flex flex-col rounded-2xl border border-ink/10 bg-surface-2 p-3 pb-3.5 shadow-sm font-sans shrink-0 w-full overflow-hidden`
- **Tones**:
  - `tone="plain"` (Default): `bg-surface-2 border-ink/10 text-ink`
  - `tone="lime"`: `bg-lime/20 border-lime/30 text-ink`
  - `tone="warn"`: `bg-amber-warn/20 border-amber-warn/30 text-ink`
  - `tone="mint"`: `bg-mint/20 border-mint/30 text-ink`
  - `tone="danger"`: `bg-danger/15 border-danger/25 text-ink`
- **Leading Icon**: `text-[16px] shrink-0 leading-none mt-0.5` (Default: `💡`)
- **Body Text**: `text-[12px] font-medium text-ink/80 leading-relaxed flex-1 pr-1 font-sans`
- **Dismiss Button**: `flex h-5 w-5 shrink-0 items-center justify-center rounded-full bg-ink/5 hover:bg-ink/15 text-[10px] font-bold text-ink/60 hover:text-ink cursor-pointer transition-colors` with label `✕`
- **10-Second Reverse Progress Bar**:
  - Track: `absolute bottom-0 left-0 right-0 h-[2.5px] bg-ink/5 overflow-hidden pointer-events-none`
  - Bar: `h-full bg-lime animate-toast-progress` (Default duration: `10000ms`, customizable or can be set to `0` / `showTimer={false}` for permanent cards)

```tsx
import { TipCard } from '@/components/kit/primitives';

<TipCard
  icon="💡"
  duration={10000}
  tone="plain"
  onClose={() => console.log('Tip dismissed')}
>
  Paling ribet kalau uang cash punya selisih tapi lupa uangnya kepake apa, biar gampang catat cepat di sini.
</TipCard>
```

---

## 4. Overlay & Feedback Component Taxonomy (The 6 Forms)

Stir strictly categorizes all overlays, dialogs, popovers, menus, and toasts into **6 standardized forms**:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        STIR OVERLAY TAXONOMY                          │
├──────────────────────────────┬─────────────────────────────────────────┤
│ Form 1: Bottom Sheet Modal   │ Form entry & mutation workflows         │
│ Form 2: Centered Modal Dialog│ Destructive confirms, guards & previews │
│ Form 3: Context Menu Popover │ Row-level contextual actions (anchored) │
│ Form 4: Dropdown Popover     │ In-place selectors, filters & pickers   │
│ Form 5: Side Drawer          │ Global navigation & dev shortcut hub    │
│ Form 6: Floating Toast       │ Transient feedback (3s auto-dismiss)    │
└──────────────────────────────┴─────────────────────────────────────────┘
```

---

### Form 1: Bottom Sheet Modal (`SheetModal` / Slide-Up Modal)

Used for **complex data entry, transaction logging, sub-drawers, and mutation forms**. Menempel di dasar layar (*bottom-anchored*) dan menggeser ke atas (*slide-up*).

#### Why Bottom Sheet for Forms?
- **Mobile Ergonomics**: Form inputs and primary CTA sit in the natural thumb reach zone.
- **Virtual Keyboard Friendly**: When the mobile soft keyboard opens, bottom sheets naturally tuck upward without obscuring active fields (unlike centered modals which get vertically truncated).

#### Specifications & CSS Tokens
- **Backdrop Overlay**:
  ```tsx
  <div className="fixed inset-0 z-50 flex flex-col justify-end items-center bg-ink/40 backdrop-blur-[1px] cursor-pointer font-sans" onClick={onClose}>
  ```
- **Sheet Container**:
  ```tsx
  <div
    className="w-full max-w-md h-auto max-h-[90vh] rounded-t-[1.6rem] overflow-hidden bg-surface p-4 shadow-2xl border border-ink/10 flex flex-col cursor-default font-sans animate-in slide-in-from-bottom duration-200"
    onClick={(e) => e.stopPropagation()}
  >
  ```
- **Corner Radius**: Strictly **`rounded-t-[1.6rem]`** (25.6px / Uang Gaib baseline). Do not use `rounded-t-[2.4rem]`. Bottom corners remain square against viewport edge.
- **Clean Header Rule**:
  - **NO top `✕` close button** and **NO drag handle pill**.
  - Header consists strictly of Title (`text-[14px] font-black text-ink`) and Subtitle (`text-[11px] font-bold text-ink/50 mt-0.5`).
  - Dismissal is performed by tapping the backdrop or tapping the bottom `Batal` button.
- **Footer Buttons**:
  ```tsx
  <div className="flex w-full gap-2 pt-3 pb-1 border-t border-ink/10 mt-3 font-sans">
    <Btn tone="ghost" className="!w-[28%] shrink-0 font-sans" onClick={onClose}>Batal</Btn>
    <Btn tone="primary" className="!w-auto flex-1 font-sans" onClick={handleSubmit}>Simpan Catatan</Btn>
  </div>
  ```
- **Live Examples in Code**:
  - `app/modal/quick-log.tsx` (`S08`)
  - `app/modal/uang-gaib.tsx` (`S10`)
  - `app/modal/tunai-burn.tsx` (`S09`)
  - `app/modal/catch-up.tsx` (`S12`)
  - `app/(tabs)/piutang.tsx` (`selectedDebtForEdit`, `selectedDebtForSettle`, `showAddUtangModal`, `selectedUtangForBayar`, `isCategoryModalOpen`)
  - `app/(tabs)/rapor.tsx` (`selectedTrophy`, `showTopUpModal`)

---

### Form 2: Centered Pop-Up Modal Dialog (`DialogModal`)

Used for **confirmation dialogs (destructive / irreversible actions)**, **system guards / warnings**, and **standalone preview cards**. Never use browser native `window.confirm()` or `window.alert()`.

#### When to Use Centered Modal Dialog:
- Deleting an entity (Hapus Transaksi, Hapus Dompet).
- Guard alerts (Saldo masih ada di dompet, Batas limit tercapai).
- Viral meme share card preview.
- **NEVER** use Centered Modal for complex multi-input forms on mobile.

#### Specifications & CSS Tokens
- **Backdrop Overlay**:
  ```tsx
  <div className="fixed inset-0 z-50 flex items-center justify-center bg-ink/40 backdrop-blur-[1px] p-4 animate-fade-in font-sans">
    <div className="fixed inset-0" onClick={onClose} />
  ```
- **Dialog Box (Confirmation / Alert)**:
  ```tsx
  <div className="relative z-10 w-full max-w-xs rounded-3xl bg-surface p-5 text-ink shadow-2xl border border-ink/10 space-y-4">
    <div className="text-center space-y-2">
      <div className="text-3xl">🗑️</div>
      <h3 className="text-[16px] font-black text-ink">Hapus Transaksi?</h3>
      <p className="text-[13px] text-ink/70 font-medium leading-snug">
        Yakin mau menghapus &quot;{name}&quot;?
      </p>
    </div>
    <div className="flex w-full gap-2 pt-2 border-t border-ink/10">
      <Btn tone="ghost" className="!w-[28%] shrink-0" onClick={onClose}>Batal</Btn>
      <Btn tone="danger" className="!w-auto flex-1" onClick={handleConfirm}>Hapus Transaksi</Btn>
    </div>
  </div>
  ```
- **Dialog Box (Card Preview)**:
  - Width: `w-[280px]` or `max-w-xs`.
  - Content: Large emoji (`text-[42px]`), title, copy, and vertical button stack (`Btn tone="dark"` + `Btn tone="ghost"`).
- **Live Examples in Code**:
  - `app/(tabs)/index.tsx` & `app/(tabs)/aktivitas.tsx` (`deletingTx`)
  - `app/governance/wallets.tsx` (`deletingWallet`)
  - `app/subscription.tsx` (`showViralCardModal`)

---

### Form 3: Anchored Context Menu Popover (`ContextMenu`)

Used when tapping or long-pressing an item in a list (e.g. transaction rows in Beranda/Aktivitas, wallet items in Kelola Dompet) to display contextual actions without leaving the screen.

#### Mechanics & Anatomy
1. **Trigger & Coordinate Capture**:
   - On row click: `const rect = e.currentTarget.getBoundingClientRect()`. Store `item` and `rect` in component state.
2. **Backdrop Overlay**:
   ```tsx
   <div className="fixed inset-0 z-40 bg-ink/40 backdrop-blur-[1px] transition-opacity cursor-pointer" onClick={() => setSelectedItem(null)} />
   ```
3. **Selected Item Highlight Clone (Z-50)**:
   - Elevated clone placed exactly above the dimmed backdrop at `rect.top`, `rect.left`, `rect.width`, `rect.height`.
   ```tsx
   <div
     className="fixed m-0 z-50 pointer-events-none rounded-xl bg-surface shadow-xl ring-2 ring-lime/40 overflow-hidden"
     style={{ top: `${rect.top}px`, left: `${rect.left}px`, width: `${rect.width}px`, height: `${rect.height}px` }}
   >
     {/* Render identical row item view */}
   </div>
   ```
4. **Dynamic Placement Calculation**:
   ```ts
   const viewportHeight = typeof window !== 'undefined' ? window.innerHeight : 800;
   const ESTIMATED_MODAL_HEIGHT = 140; // compact height without redundant header
   const GAP = 8;
   const spaceBelow = viewportHeight - rect.bottom;
   const spaceAbove = rect.top;
   const placeBelow = spaceBelow >= ESTIMATED_MODAL_HEIGHT || spaceBelow >= spaceAbove;
   ```
5. **Floating Action Box & Placement-Aware Direction**:
   - Headerless: Avoid duplicating row content or close buttons inside the popover since the elevated highlight clone is already visible and backdrop click dismisses.
   - Dynamic ordering: Primary/Edit action is always placed closest to the tapped row (`placeBelow ? '' : 'flex-col-reverse'`).
   ```tsx
   <div
     style={popoverStyle}
     onClick={(e) => e.stopPropagation()}
     className="w-full max-w-sm rounded-2xl bg-surface p-2 shadow-2xl border border-ink/10 animate-in fade-in zoom-in-95 duration-150"
   >
     <div className={`flex flex-col gap-1.5 ${placeBelow ? '' : 'flex-col-reverse'}`}>
       {/* Primary Action Rows: rounded-xl bg-surface-2 hover:bg-ink/5 text-[12px] font-bold text-ink */}
       {/* Destructive Action Rows: bg-danger/10 hover:bg-danger/20 text-[12px] font-bold text-danger border border-danger/20 */}
     </div>
   </div>
   ```
- **Live Examples in Code**:
  - `app/(tabs)/index.tsx` (Beranda row action popover)
  - `app/(tabs)/aktivitas.tsx` (Aktivitas row action popover)
  - `app/governance/wallets.tsx` (Wallet action popover)

---

### Form 4: Anchored Dropdown Popover (`DropdownPopover`)

Used for **in-place filters, quick selectors, and status switchers** anchored relative to a trigger button.

#### Specifications & CSS Tokens
- **Trigger Container**: `relative inline-block`
- **Click-Outside Interceptor**:
  ```tsx
  <div className="fixed inset-0 z-30 cursor-default" onClick={() => setIsOpen(false)} />
  ```
- **Dropdown Panel**:
  ```tsx
  <div className="absolute right-0 top-full mt-1.5 min-w-[140px] rounded-2xl bg-surface p-2 shadow-xl border border-ink/10 z-40 font-sans animate-in fade-in zoom-in-95 duration-100 space-y-1">
  ```
- **Option Item**:
  - Default: `w-full text-left px-3 py-1.5 text-[12px] font-bold rounded-xl text-ink hover:bg-ink/5 transition-colors`
  - Active: `bg-lime/25 text-lime-deep font-extrabold`
- **Live Examples in Code**:
  - Month Selector in Beranda (`index.tsx:L291`)
  - Month Selector in Rapor (`rapor.tsx:L250`)
  - Multi-Select Wallet Filter in Aktivitas (`aktivitas.tsx:L641`)
  - Date Preset Filter in Aktivitas (`aktivitas.tsx:L719`)
  - Data Mode Switcher (`components/DataModeBadge.tsx:L63`)
  - Account Selector in Piutang settlement (`piutang.tsx:L900`)

---

### Form 5: Side Drawer Navigation (`SideDrawer`)

Used for **global navigation drawer, side menu, and developer/testing shortcut hubs**.

#### Specifications & CSS Tokens
- **Container**: `fixed inset-0 z-50 flex`
- **Backdrop**:
  ```tsx
  <div className="fixed inset-0 bg-ink/40 backdrop-blur-[1px] transition-opacity duration-200" onClick={closeSideMenu} />
  ```
- **Drawer Panel**:
  ```tsx
  <div className="relative z-50 w-[85%] max-w-xs h-full bg-surface text-ink border-r border-ink/15 shadow-2xl flex flex-col overflow-hidden animate-in slide-in-from-left duration-200">
  ```
- **Header**: Avatar, title (`text-[13px] font-black`), subtitle (`text-[10px] font-semibold text-ink/50`), and rounded dismiss button (`✕`).
- **Live Examples in Code**:
  - `components/SideMenu.tsx` (Testing Shortcut Hub across 16 screens)

---

### Form 6: Floating Bottom Toast (`Toast`)

Used for **transient confirmation feedback** after a mutation (e.g. data saved, item deleted, link copied). Auto-dismisses after **3000ms**.

#### Standard Toast Rule
- **Single Uniform Design**: All notifications must strictly use the **Floating Bottom Pill** format via the `<Toast>` primitive from `components/kit/primitives.tsx`.
- **Reverse Progress Bar Animation**: Every toast features an integrated bottom countdown bar (`h-[3px] bg-lime`) that linearly drains from 100% to 0% across the 3000ms duration (`animate-toast-progress`).
- **Deprecation**: Do NOT render inline lime banners (`mb-3 bg-lime`) inside document flow as they cause jarring layout shifts (content jumping up/down). Do NOT render top-right floating toasts.
- **Placement**: Floats at `bottom-20` (80px from bottom, directly above the fixed bottom navigation tab bar) horizontally centered with `z-50`.

#### Specifications & Reusable Primitive
```tsx
import { Toast } from '../../components/kit/primitives';

// Standard usage:
<Toast message={toastMsg} onClose={() => setToastMsg(null)} duration={3000} />
```

#### Underlying CSS Tokens
```tsx
<div
  role="status"
  aria-live="polite"
  className="fixed bottom-20 left-1/2 -translate-x-1/2 z-50 flex flex-col overflow-hidden rounded-2xl bg-ink text-surface shadow-2xl border border-surface/20 animate-in fade-in slide-in-from-bottom-2 duration-200 font-sans pointer-events-auto min-w-[240px] max-w-[90vw]"
>
  <div className="flex items-center justify-between gap-3 px-4 py-2.5 text-[12px] font-bold">
    <span>{toastMsg}</span>
    <button
      type="button"
      onClick={() => setToastMsg(null)}
      className="text-xs font-black ml-1 text-surface/70 hover:text-surface cursor-pointer shrink-0"
    >
      ✕
    </button>
  </div>
  <div className="h-[3px] w-full bg-surface/15 overflow-hidden">
    <div
      className="h-full bg-lime animate-toast-progress"
      style={{ animationDuration: "3000ms" }}
    />
  </div>
</div>
```

---

### Form 1B: FAB Speed Dial Pattern (`FabSpeedDial`)

Used as the **universal entry point for all creation and fast-logging actions** accessible from anywhere in the app.

#### Placement Variants:
- **`variant="docked"` (Center Tab Bar)**: Embedded in the center notch of `SharedTabBar` (`absolute -top-5 left-1/2 -translate-x-1/2`).
- **`variant="floating"` (Floating Right)**: Fixed at `fixed bottom-6 right-6 z-40` for standalone views.

#### Visual Tokens & Anatomy (Speed Dial Reference):
- **Backdrop**: `fixed inset-0 z-40 bg-black/60 backdrop-blur-[2px]` (auto-dismiss on tap or `Escape`).
- **FAB Trigger**: Signature lime button (`bg-lime text-lime-deep`) rotating 90° into a dark elevated close button (`bg-[#242731] text-white ring-2 ring-lime/50 shadow-2xl`).
- **Circular Action Buttons**: `w-11 h-11 rounded-full shadow-lg` with vivid background colors and bespoke flat SVG outline icons (24x24 viewBox, stroke-width 2.2-2.3, round caps/joins):
  - **Pengeluaran** (`bg-rose-500`, `shadow-rose-500/30`): `<PengeluaranFabIcon />` — Outflow Coin (circular coin border with diagonal outgoing arrow `↗` replacing foreign dollar symbol).
  - **Pemasukan** (`bg-emerald-500`, `shadow-emerald-500/30`): `<PemasukanFabIcon />` — Banknote (crisp flat cash bill with center seal and security dots).
  - **Transfer** (`bg-sky-500`, `shadow-sky-500/30`): `<TransferFabIcon />` — Symmetric Exchange Arrows (balanced right `→` and left `←` kinetic arrows).
  - **Catat Utang** (`bg-purple-500`, `shadow-purple-500/30`): `<CatatUtangFabIcon />` — PayLater Card (credit/liability card with magnetic stripe and chip).
  - **Catat Piutang** (`bg-amber-500`, `shadow-amber-500/30`): `<CatatPiutangFabIcon />` — Hand & Coin (open palm offering coin forward to a friend).
  - **Input Teks** (`bg-lime text-lime-deep`, `shadow-lime/40`): `<InputTeksFabIcon />` — Chat Prompt Bubble (rounded message bubble with horizontal text lines for AI log input).
- **Floating Pill Badges**: Rectangular rounded pill labels (`bg-[#242731] text-white font-bold text-[12px] px-3.5 py-1.5 rounded-xl border border-white/10 shadow-xl`) placed to the left of each circular button with subtle hover elevation.
- **Staggered Animation**: Items fan out vertically with a 25ms delay per item (`slide-in-from-bottom-2 duration-150`).

---

## 5. Developer Decision Matrix (Which Form to Choose?)

When adding a new interaction or screen flow, consult this quick decision matrix:

| User Scenario / Intent | Component Form to Use | Key Reason |
|---|---|---|
| Creating a transaction, logging cash, recording debt, editing debt/payment | **Form 1: Bottom Sheet Modal** | Needs comfortable keyboard space, thumb reach, multi-field inputs. |
| Deleting an item, resetting a balance, irreversible action | **Form 2: Centered Modal Dialog** | Forces deliberate focus to prevent accidental destructive taps. |
| Tapping a row in a list to choose between Edit, Split, or Delete | **Form 3: Anchored Context Menu** | Keeps spatial context of the selected row with an elevated visual highlight. |
| Filtering by date, selecting a wallet, picking a month | **Form 4: Anchored Dropdown Popover** | Compact single/multi-selection directly anchored to the trigger button. |
| Switching routes, accessing system testing shortcuts | **Form 5: Side Drawer Navigation** | Full-height off-canvas panel for high-density navigation. |
| Notifying user that an item was created, updated, deleted, or copied | **Form 6: Floating Bottom Toast** | Instant 3-second non-blocking feedback without pushing or shifting page content. |

---

## 6. Living Contract & Enforcement

1. **Backdrop Consistency**: All modal and popover backdrops must strictly use `bg-ink/40 backdrop-blur-[1px]`.
2. **Button Sizing Consistency**: Any bottom action footer with Cancel + Confirm must strictly follow `!w-[28%] shrink-0` (ghost tone) and `!w-auto flex-1` (primary/danger tone).
3. **No Unstyled Elements**: Never use raw HTML buttons or unstyled `div` overlays. All components must adhere to the tokens defined in this guideline.

---

## 7. Category Color & Icon Customization Patterns

### 40-Shade Tonal Palette
Categories use a structured matrix of **40 colors** arranged into **8 main hue families × 5 tonal shades** (Deep Dark, Dark, Base, Light, Thin Light):
- **Red Family**: `#7F1D1D`, `#B91C1C`, `#EF4444`, `#F87171`, `#FCA5A5`
- **Orange Family**: `#7C2D12`, `#C2410C`, `#F97316`, `#FB923C`, `#FDBA74`
- **Amber / Yellow Family**: `#78350F`, `#B45309`, `#F59E0B`, `#FBBF24`, `#FDE68A`
- **Green Family**: `#14532D`, `#15803D`, `#22C55E`, `#4ADE80`, `#86EFAC`
- **Teal / Cyan Family**: `#134E4A`, `#0F766E`, `#14B8A6`, `#2DD4BF`, `#99F6E4`
- **Blue Family**: `#1E3A8A`, `#1D4ED8`, `#3B82F6`, `#60A5FA`, `#93C5FD`
- **Purple / Indigo Family**: `#312E81`, `#4338CA`, `#6366F1`, `#818CF8`, `#A5B4FC`
- **Pink / Rose Family**: `#831843`, `#BE185D`, `#EC4899`, `#F472B6`, `#F9A8D4`

*Note: The Dark Grey background (`#374151`) is reserved exclusively for the system-defined "Other" category (🪙) and excluded from selectable user palettes.*

### Strict Non-Alphabet Emoji Validator
Category icon customization allows selecting from a curated emoji grid or typing/pasting into a custom input. The input strictly filters out and strips Latin alphabet letters (`[a-zA-Z]`) while accepting unicode emojis and symbols (max 2 glyphs):
```ts
export function validateAndCleanEmoji(input: string): string {
  if (!input) return '';
  let cleaned = input.replace(/[a-zA-Z]/g, '');
  const emojiRegex = /(\p{Emoji_Presentation}|\p{Extended_Pictographic})/gu;
  const matches = cleaned.match(emojiRegex);
  if (matches && matches.length > 0) return matches.slice(0, 2).join('');
  const nonAlpha = cleaned.replace(/[\s\r\n\t]/g, '');
  return nonAlpha.slice(0, 2);
}
```

---

## 8. Financial Reporting Hub & Analytics Design Patterns (`/rapor`)

The Rapor reporting suite (`app/(tabs)/rapor.tsx`, `components/reports/*`, `lib/reportEngine.ts`) provides analytical insights across 5 dedicated reporting tabs: **Pengeluaran**, **Pemasukan**, **Arus Kas**, **Tren Saldo**, and **Ringkasan**.

### Visual Archetype & Dark Chart Theme
- **Container**: Dark navy-slate container (`bg-[#131922] rounded-[28px] border border-white/5 p-4`).
- **Hero Typography**: Bold uppercase tracked title (`text-[10px] tracking-widest font-black text-white/50`), bold white currency (`text-[26px] font-black tracking-tight text-white`), and formatted nominals with dot thousand separators (`id-ID`).
- **Pill Badges**: Dark tonal pills with contextual indicators (`bg-white/10 text-[10px] font-bold text-white/80 rounded-full px-2.5 py-1`).

### Interactive Bar & Trendline Chart (`BarTrendChart`)
- **Anchored Average Reference Badge**: Pinned to the right edge of the chart container (`div.absolute.right-2`) so it remains permanently visible during horizontal scrolling in daily mode.
- **Dynamic Comparative Indicators**: Active bar selection formats sub-metrics with prefixed comparison arrows (`[arrow] Rata-rata: Rp ... | [arrow] Tren: Rp ...`).
- **Contextual Financial Polarity Colors**:
  - Expense Mode: Lower than average/trend = Green (`#bef264` / favorable); Higher than average/trend = Red (`#f87171` / unfavorable).
  - Income Mode: Higher than average/trend = Green (`#bef264` / favorable); Lower than average/trend = Red (`#f87171` / unfavorable).
- **Date & Month Presentation**:
  - Daily mode: Full Indonesian date on selection (`3 SEPTEMBER 2026`).
  - Monthly mode: Full Indonesian month names (`JULI`, `SEPTEMBER`).
  - MoM / YoY comparison badge: Contextual text (`"dari bulan lalu"` / `"dari tahun lalu"`).

### Category Donut & Tree Hierarchy Breakdown (`CategoryPieReport`)
- **Top 5 Slice Capping**: Donut chart and legend are capped to a maximum of 5 slices (Top 4 parent categories + remaining items grouped into a grey `#94a3b8` `"Lainnya"` slice).
- **Comprehensive Expanded Breakdown**: Tapping "Rincian Sub-Kategori" expands the list to display **ALL** parent categories and subcategories that have active transactions.
- **Tree Hierarchy Visual Connector**:
  - Vertical branch lines and corner paths (`border-l-2 border-b-2 border-ink/20 rounded-bl-lg`) visually connect parent category headers to child subcategory cards.
- **Subcategory Row Layout & Parent-Relative Metrics**:
  - Subcategory percentage is positioned on the **far left** inside the row card (`[ 84% ] [ Belanja & Fashion ]` $\leftrightarrow$ `[ Rp 999.500 ]`).
  - Percentage is calculated **relative to parent category total** ($$P_{\text{sub}} = \frac{\text{Amount}_{\text{sub}}}{\text{Amount}_{\text{parent}}} \times 100\%$$), with row background progress fills (`bg-lime/20`) scaling relative to the parent.
  - Parent overall percentage badge is displayed on the **far right** of the parent header card (`Lifestyle` $\leftrightarrow$ `36%`).

### Pixel-Perfect Period & Granularity Segmented Pills
- **Fixed Height Box-Border**: Locked to `h-[36px]` container height with `h-[26px]` inner interactive buttons across all granularity modes (`Harian` with `[ Sep ▾ | 2026 ▾ ]`, `Bulanan` with `[ 2026 ▾ ]`, and `Tahunan` with `[ Multi-Tahun ]`), eliminating vertical jumping and layout shifts during tab switching.
- **Descending Selection Order**: Month dropdown sorted from newest to oldest (Desember down to Januari); Year dropdown sorted newest to oldest (2026, 2025, ...).

### Two-Column Filter Bar & 2-Tap Category Verification (`ReportFilterBar`)
- **Grid Layout**: 2-column layout matching Aktivitas (`grid grid-cols-2 gap-2`).
- **Multi-Wallet Dropdown**: Multi-select wallet filter with section dividers (`h-4 w-px bg-ink/10`) and clean brand logos.
- **2-Tap Category Verification**: Category dropdown tree requires an explicit `"OK (Terapkan)"` commit to prevent premature chart re-renders.

### Timezone Integrity Engine (`lib/reportEngine.ts`)
- **Local Date Formatting**: Replaced UTC ISO string splitting with calendar date helpers `formatLocalDateStr(year, month, day)` and `getTxLocalDateStr(date)` to prevent UTC+7 date-shifting bugs.
- **Mathematical Invariance**: 100% mathematical matching between BarTrendChart hero metrics and CategoryPieReport breakdowns across all granularity modes.
