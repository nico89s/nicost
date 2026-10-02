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
│   ├── SECURITY_MODEL.md            # Intended security design, threat actors, trust boundaries
│   ├── CHANGELOG.md                 # Product-level change history
│   ├── audit/                       # Project assurance & compliance audit suite
│   │   ├── AUDIT_SCOPE.md           # Project audit manifest (YAML flags + scope)
│   │   ├── AUDIT_REPORT.md          # 6-state consolidated executive audit report
│   │   └── findings/                # Reusable AUD-<DOMAIN>-<ID> findings
│   │       └── FINDING_TEMPLATE.md
│   ├── user-stories/                # Granular user stories with Gherkin ACs
│   │   ├── README.md                # Story conventions and index
│   │   └── US-001-template.md       # User story template with test matrix
│   ├── flows/                       # Mermaid user journeys & reachability specs
│   │   └── flow-template.md
│   └── rules/                       # Business rules, domain invariants, and calculations
│       └── BUSINESS_RULES.md
│
├── .agents/
│   └── skills/                      # Portable procedural & assurance skills
│       ├── auditor/SKILL.md         # Master assurance orchestrator protocol
│       ├── audit-security/SKILL.md   # OWASP MASVS L1, auth, RLS, secrets
│       ├── audit-privacy/SKILL.md    # Apple & Google privacy manifests
│       ├── audit-supply-chain/SKILL.md # Lockfiles, SBOM, dependencies
│       ├── audit-legal-ip/SKILL.md   # License scanning & AI code provenance
│       ├── audit-platform/SKILL.md   # Store review guidelines & policies
│       ├── audit-regional/SKILL.md   # Indonesian UU PDP, PSE, QRIS & GDPR
│       ├── audit-adversarial/SKILL.md # Entitlement bypasses, IDOR, abuse
│       ├── audit-release/SKILL.md    # Production artifacts & release gate
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
   - `docs/audit/AUDIT_SCOPE.md`: Configure your project audit scope manifest, target platforms, regions, and active modules during Phase 1.
   - `docs/SECURITY_MODEL.md`: Define intended security design and threat boundaries during Phase 4.
   - `docs/ARCHITECTURE.md`: Specify your tech stack, database, and repository layout.
   - `docs/DESIGN_GUIDELINES.md`: Define your typography, color tokens, and UI components.
   - `AGENTS.md`: Update project name and verification commands (e.g. `npm test`, `tsc`).
3. **Customize baseline skills**:
   - Update `.agents/skills/frontend/SKILL.md` for your specific UI framework (e.g. Next.js / Tailwind / Flutter).
   - Update `.agents/skills/debugging/SKILL.md` with your local run and log commands.
   - Update `.agents/skills/testing/SKILL.md` with your test runner.
4. **Start building with AI agents**:
   - Every AI agent will automatically load `AGENTS.md` and respect your `/docs/` sources of truth.
