# Stir — Indonesian-Native AI Personal Finance Manager

Stir is a zero-friction personal finance ecosystem tailored specifically to Indonesian spending behavior, payment fragmentation, and social dynamics.

---

## 🧭 Repository Standards & Documentation

This repository follows the **Universal AI Agent Architecture**. All documentation is structured as the single source of truth:

- **AI Agent Constitution**: [`AGENTS.md`](AGENTS.md) — Mandatory working rules and boundaries for all AI tools.
- **Developer Preferences**: [`DEVELOPER.md`](DEVELOPER.md) — Universal personal collaboration preferences and style.
- **Product Requirements**: [`docs/PRD.md`](docs/PRD.md) — Vision, features, and core mechanics.
- **UI & Design System**: [`docs/DESIGN_GUIDELINES.md`](docs/DESIGN_GUIDELINES.md) — Primitives, modal specs, tokens, and typography.
- **System Architecture**: [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — Tech stack, data flow, runtimes, and security boundaries.
- **Product Changelog**: [`docs/CHANGELOG.md`](docs/CHANGELOG.md) — High-level product and requirement changes.
- **User Stories & Acceptance Criteria**: [`docs/user-stories/`](docs/user-stories/) — Granular Gherkin stories and test matrices.
- **User Flows & Route Map**: [`docs/flows/`](docs/flows/) — 16-screen route map and Mermaid flowcharts.
- **Business Rules & Invariants**: [`docs/rules/`](docs/rules/) — Financial precision, social ledger, and reconciliation rules.
- **Project Skills**: [`.agents/skills/`](.agents/skills/) — Procedural workflows for frontend, debugging, testing, and accounting integrity.
- **Flow Auditor Agent**: [`.github/agents/flow-auditor.md`](.github/agents/flow-auditor.md) — Independent reachability and navigation auditor.

---

## 🚀 Quickstart & Development

### 1. Web Localhost Showcase
```bash
# Launch rapid web development showcase
npm run dev
# Or with Expo web:
npx expo start --web
```

### 2. Type Checking
```bash
./node_modules/.bin/tsc --noEmit
```

### 3. Mobile Device Testing (Expo Go)
```bash
npx expo start
```
Scan the terminal QR code using **Expo Go** on Android.
