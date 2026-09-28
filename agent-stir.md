# Repository Agent Contract

This repository contains Stir, an early-stage Indonesian personal-finance prototype. Read [PROJECT_CONTEXT.md](PROJECT_CONTEXT.md) before substantial work, then load only the documentation or skill relevant to the task.

## Working rules

- Inspect the affected implementation, schema, configuration, and tests before editing. Use documentation for orientation, not as a substitute for repository evidence.
- When requirements, current code, and documentation disagree, surface the conflict. Do not silently force current code to match an older design document.
- Prefer the smallest coherent change. Preserve unrelated work, comments, reference assets, and established interfaces.
- Ask before destructive operations, major architectural changes, new screens outside the agreed product model, or introducing a new state-management, native styling, E2E, or backend-hosting strategy.
- Do not modify `reference-kit/`; it is a frozen visual baseline. Treat `docs/prd/` as historical product intent unless current documentation explicitly says otherwise.
- Do not edit generated artifacts such as `dist/`. Never expose service-role keys or other privileged secrets to client code.
- Database changes must preserve tenant isolation, RLS, financial precision, and existing data. Inspect both active SQL and calling code. The repository does not yet have a migration or generated-types workflow, so propose one rather than inventing it during an unrelated task.
- Debug by reproducing and gathering evidence before changing code. Avoid timing hacks, swallowed errors, and unrelated speculative edits.
- Verify the behavior changed before claiming completion. Report commands run, outcomes, and any checks that could not be performed.
- **Documentation Changelog**: Whenever modifying any documentation file (such as PRDs, domain models, system architecture, design specs, or user stories), include or update a `## Changelog` section at the top of the document with date (`YYYY-MM-DD`) and overview of changes. Whenever product flows or acceptance criteria change, update the corresponding User Story in `docs/user-stories/`.

## UI & Design System Rules (Strict Consistency)

- **Centralized Primitives**: Always reuse components from `components/kit/primitives.tsx` (`<Btn>`, `<Card>`, `<Sheet>`, `<Pill>`, `<Label>`, `<Title>`, `<Sub>`). Do NOT write raw `<button>` elements or custom `<div onClick>` containers with inline rounded classes.
- **Button Rounding (`Btn`)**: Main CTA buttons must strictly use `rounded-2xl` (16px radius). Do not apply `rounded-full` to narrow action buttons as it turns them into squished ovals.
- **Pills & Tab Containers**: Selection chips, pill tags, and top tab switchers must use `rounded-full` (`flex rounded-full bg-surface-2 p-1`).
- **Multi-Button Rows**: For side-by-side button layouts (e.g. Batal + Simpan Transaksi), use explicit flex sizing (`!w-[28%] shrink-0` for secondary actions and `!w-auto flex-1` for primary actions inside `flex w-full gap-2 pt-3 pb-1 border-t border-ink/10`) to guarantee buttons fill 100% of container width without shrinking.
- **Slide-Up Modal Baseline**:
  - **Corner Radius**: `rounded-t-[1.6rem]` (25.6px / Uang Gaib style). Avoid `rounded-t-[2.4rem]`.
  - **Dynamic Height**: `h-auto max-h-[90vh]` so sheet height fits content dynamically.
  - **Clean Header**: No top `✕` close button and no drag handle bar. Tap backdrop or click bottom `Batal` button to close.
  - **Dismiss Button**: Always use `Batal` with `tone="ghost"` (`border border-ink/15 text-ink/70`) at `!w-[28%] shrink-0`. Never use solid red (`tone="danger"`) for modal dismissal.
  - **Typography (Modals)**: Use `font-sans` (Plus Jakarta Sans) exclusively for modal text/inputs, minimum font size `text-[11px]` (floor for labels/subtext), and main input/text at `text-[14px]`.
  - **Money Formatting**: Every money amount MUST use dot thousand separators (`id-ID`).
- **Single Visual Baseline**: Refer to `reference-kit/src/components/kit/primitives.tsx` and `reference-kit/src/components/kit/screens.tsx` as the single visual baseline for all components and screen layouts.
- **Detailed UI Specifications**: For full mechanics of the 6 standardized overlay forms (Bottom Sheet Modal, Centered Modal Dialog, Anchored Context Menu Popover, Anchored Dropdown Popover, Side Drawer Navigation, and Floating Bottom Toast), developer decision matrix, typography scale, and CSS tokens, see [docs/design/ui-design-rules.md](docs/design/ui-design-rules.md).
- **Living Contract**: These UI rules can and should be updated as new screens, primitives, or design patterns are introduced to the codebase.

## Current verification baseline

- Type-check: `./node_modules/.bin/tsc --noEmit`
- Expo development: `npm start`; platform variants are defined in `package.json`.
- `npm run lint` is declared, but lint tooling is not currently established locally. Do not claim lint passed unless the command completes without scaffolding or modifying configuration.
- There is no formal test runner, `test` script, or `build` script. Files under `scripts/` are ad-hoc checks; the Supabase checks use a live external project.

Use `.agents/skills/testing/SKILL.md` to choose proportional validation rather than treating every available command as mandatory.

## Context routing

- Product terms and financial invariants: `docs/product/domain-model.md`
- Architecture and sensitive code paths: `docs/architecture/system-overview.md`
- UI & Design System Rules: `docs/design/ui-design-rules.md`
- Navigation wireframe & 16-screen route map: `docs/architecture/navigation-wireframe.md`
- Active decisions and known compromises: `docs/decisions/architecture-decisions.md`
- Honest implementation status, commands, and gaps: `docs/development/current-state.md`
- Specialized procedures: `.agents/skills/`
