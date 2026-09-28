# Developer Preferences

This file records reusable collaboration preferences supported by direct instructions. Stir-specific product, UI, database, and financial rules belong in project documentation rather than in a personal preference profile.

## Established preferences

- Present a proposal and wait for approval before broad multi-file, documentation-suite, or architectural changes.
- Treat explicit negative constraints as hard scope boundaries.
- Describe the repository honestly. Distinguish working production behavior from prototypes, fallbacks, simulations, and plans.
- Inspect relevant implementation and configuration before proposing or editing code.
- Prefer focused, non-destructive changes and pragmatic simplicity over premature abstraction.
- Surface meaningful conflicts and assumptions instead of resolving them silently.
- Diagnose root causes before applying fixes; avoid timing workarounds and superficial error suppression.
- Verify changes proportionally and report both completed and unavailable checks.
- Communicate concisely with scannable structure and concrete status language when it improves clarity.
- Propose significant architecture choices before implementation when the existing direction is unsettled.

## Not personal defaults

The following are project-owned and must not be generalized to unrelated repositories:

- IDR formatting and `rb`/`jt` input;
- Supabase schema and RLS choices;
- Expo/Vite dual-runtime behavior;
- DOM-to-native component migration;
- the sixteen-screen baseline and `reference-kit/` protection;
- Stir typography, color, localization, and financial invariants.

## Still unknown

Do not infer a fixed preference for state management, native styling, mobile E2E tooling, background-job hosting, or Git/PR conventions. Ask when a task requires one of these decisions.
