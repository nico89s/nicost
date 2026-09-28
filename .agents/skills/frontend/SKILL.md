---
name: frontend
description: Build, modify, or refactor user interfaces, screens, and modals in Stir using the design system, kit primitives, and Tailwind/NativeWind rules.
---

# Frontend Skill — Stir UI & Components

## Objective
Ensure all user interfaces, screens, modals, and input forms strictly conform to the Stir UI & Design System Guidelines (`docs/DESIGN_GUIDELINES.md`) and the visual baseline in `reference-kit/`.

---

## 1. Authoritative Guidelines

Before creating or editing any UI component:
1. Inspect [`docs/DESIGN_GUIDELINES.md`](../../../docs/DESIGN_GUIDELINES.md).
2. Inspect existing primitives in `components/kit/primitives.tsx`.
3. Check reference screens in `components/kit/screens.tsx` and `reference-kit/`.

---

## 2. Mandatory Component Primitives

Never write raw HTML `<button>`, unstyled inputs, or custom `<div>` clickable boxes. Always use:

| Primitive | Purpose | Key Props & Styling |
|---|---|---|
| `<Btn>` | All action buttons | `tone="primary" \| "ghost" \| "danger" \| "success"` |
| `<Card>` | Container blocks | Surface background, subtle border, rounded-2xl |
| `<Sheet>` | Slide-up modals | `rounded-t-[1.6rem]`, dynamic height, backdrop |
| `<Pill>` | Tag, status, chip | `rounded-full`, compact padding |
| `<Label>` | Field titles & labels | `text-[11px] font-bold text-ink/50` |
| `<Title>` | Section/modal headings | Plus Jakarta Sans, font-black / font-bold |
| `<Sub>` | Explanatory subtext | `text-[11px]` to `text-[12px]`, muted tone |

---

## 3. Strict Button & Action Rules

- **CTA Buttons**:
  - Must strictly use `rounded-2xl` (16px radius).
  - Never use `rounded-full` on rectangular action buttons.
- **Pills & Switchers**:
  - Chips, category filters, and segment switchers use `rounded-full` (`bg-surface-2 p-1`).
- **Multi-Button Rows (e.g. Batal + Simpan)**:
  - Container: `flex w-full gap-2 pt-3 pb-1 border-t border-ink/10`
  - Secondary / Dismiss button (`Batal`): `!w-[28%] shrink-0` with `tone="ghost"`
  - Primary CTA (`Simpan`): `!w-auto flex-1` with `tone="primary"`

---

## 4. Standard Slide-Up Modal Contract

Every bottom sheet modal MUST implement these exact specifications:
- **Top Corners**: `rounded-t-[1.6rem]` (25.6px radius). Avoid `rounded-t-[2.4rem]`.
- **Height**: `h-auto max-h-[90vh]` so the sheet hugs content dynamically.
- **Header**: Clean header with title only. No top `✕` close button and no drag handle bar.
- **Dismissal**:
  - Backdrop tap dismisses modal.
  - Bottom `Batal` button dismisses modal.
  - Never use `tone="danger"` for modal dismissal.
- **Typography**:
  - Exclusive font: Plus Jakarta Sans (`font-sans`).
  - Absolute minimum font size: `text-[11px]`.
  - Main input/value: `text-[14px] font-bold text-ink`.
- **Currency Display**:
  - Format all money values with period thousand separators: `amount.toLocaleString('id-ID')`.

---

## 5. Universal React Native Guardrails

- For cross-platform files under `components/` and `app/`:
  - Use `<View>`, `<Text>`, `<TouchableOpacity>`, `<ScrollView>`, `<Image>` instead of web-only HTML tags.
  - Use Tailwind/NativeWind utility classes supported by React Native.
- Avoid libraries requiring native pod installation; use official `@expo/...` SDKs.
