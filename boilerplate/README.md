# Universal Project Starter Kit & AI Agent Boilerplate

This starter template provides the standard documentation, AI agent configuration, and quality assurance framework for new software projects.

It establishes a predictable pair-programming environment across **Google Antigravity**, **GitHub Copilot**, **Claude Code**, and other LLM agent tools.

---

## 📁 Template Structure

```text
my-new-project/
├── AGENTS.md                         # Universal Agent Constitution (Standing rules for all AI tools)
├── DEVELOPER.md                      # Developer Preferences & Working Persona
├── README.md                         # Project overview and quickstart
│
├── docs/                             # Single Source of Truth for Product & Architecture
│   ├── PRD.md                       # Product Requirements Document
│   ├── DESIGN_GUIDELINES.md         # UI/UX Design System, tokens, and components
│   ├── ARCHITECTURE.md              # Technical Architecture, stack, data flows, security
│   ├── CHANGELOG.md                 # Product-level change history
│   ├── user-stories/                # Granular user stories with Gherkin ACs
│   │   ├── README.md                # Story conventions and index
│   │   └── US-001-template.md       # User story template with test matrix
│   ├── flows/                       # Mermaid user journeys & reachability specs
│   │   └── flow-template.md
│   └── rules/                       # Business rules, domain invariants, and calculations
│       └── BUSINESS_RULES.md
│
├── .agents/
│   └── skills/                      # Portable procedural skills
│       ├── frontend/SKILL.md        # UI standards, component reuse, and styling
│       ├── debugging/SKILL.md       # Diagnostic protocol and commands
│       └── testing/SKILL.md         # AC verification and test reporting
│
└── .github/
    └── agents/
        └── flow-auditor.md          # Independent reachability & discovery auditor
```

---

## ⚡ How to Initialize a New Project

1. **Copy this directory** into your new project root:
   ```bash
   cp -R /path/to/nicost/boilerplate/. /path/to/my-new-project/
   ```
2. **Fill in the project placeholders**:
   - `docs/PRD.md`: Define the problem, target audience, and core features.
   - `docs/ARCHITECTURE.md`: Specify your tech stack, database, and repository layout.
   - `docs/DESIGN_GUIDELINES.md`: Define your typography, color tokens, and UI components.
   - `AGENTS.md`: Update project name and verification commands (e.g. `npm test`, `tsc`).
3. **Customize baseline skills**:
   - Update `.agents/skills/frontend/SKILL.md` for your specific UI framework (e.g. Next.js / Tailwind / Flutter).
   - Update `.agents/skills/debugging/SKILL.md` with your local run and log commands.
   - Update `.agents/skills/testing/SKILL.md` with your test runner.
4. **Start building with AI agents**:
   - Every AI agent will automatically load `AGENTS.md` and respect your `/docs/` sources of truth.
