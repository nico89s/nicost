# UI & Design System Guidelines

## Changelog
- **YYYY-MM-DD**: Initial design guidelines established.

This document serves as the single source of truth for UI patterns, styling tokens, component mechanics, and interaction rules.

---

## 1. Design Principles

1. **Clarity Over Decoration**: Interfaces must prioritize legibility and user action over ornate visual effects.
2. **Consistency Over Novelty**: Prefer reusing established patterns before creating bespoke components.
3. **Immediate Feedback**: Every user interaction (press, submit, error) must produce perceptible feedback.
4. **Discoverable Actions**: Primary goals must remain prominent and reachable within the viewport.

---

## 2. Visual Tokens & Scales

### Typography Scale
- **Display / Hero**: `text-[28px]` to `text-[36px]` font-black tracking-tight
- **Section Headers**: `text-[18px]` to `text-[22px]` font-bold
- **Card / Group Titles**: `text-[15px]` to `text-[16px]` font-bold
- **Body / Main Inputs**: `text-[14px]` font-medium
- **Labels / Auxiliary**: `text-[12px]` to `text-[13px]` font-medium
- **Minimum Font Floor**: `text-[11px]` (Never use sub-11px micro-text)

### Spacing & Grid Scale
- Base unit: `4px` (`p-1`, `p-2`, `p-3`, `p-4`, `p-6`, `p-8`)

### Color Palette & Semantics
- `surface-0`: Main page background
- `surface-1`: Primary card background
- `surface-2`: Inset containers, chips, and muted controls
- `ink`: Primary typography
- `ink/70`: Secondary typography
- `ink/40`: Muted / placeholder text
- `primary`: Brand accent and primary CTA
- `danger`: Destructive actions and critical validation
- `success`: Confirmations and positive metrics

---

## 3. Core Component Primitives

Every screen must compose existing primitives:
- `<Button>`: Primary action buttons (`rounded-2xl` or project standard)
- `<Card>`: Content blocks with standardized padding and subtle border
- `<Modal>` / `<Sheet>`: Overlay forms with clean headers and explicit dismiss actions
- `<Pill>` / `<Chip>`: Category tags and filter toggles (`rounded-full`)
- `<Input>`: Standardized input container with floating or clear labels

---

## 4. Navigation & View Transitions

- Primary navigation: Tab bar or side navigation
- Secondary navigation: Modal sheets or pushed subviews
- Dismissal: Back button or explicit cancel action; tap backdrop for modals

---

## 5. Prohibited Patterns

- Never write inline custom styles that bypass the design token system.
- Never introduce micro font sizes below the 11px floor.
- Never place destructive actions without a confirmation or clean cancel option.
