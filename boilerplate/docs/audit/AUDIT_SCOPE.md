---
schema_version: "1.0"
app_name: "[App Name]"
app_type: "mobile" # mobile | web | fullstack | api | cli | extension
platforms:
  - "ios"
  - "android"
  - "web"
distribution_channels:
  - "apple_app_store"
  - "google_play"
  - "web_saas"
target_regions:
  - "ID"
  - "GLOBAL"
threat_level: "standard" # low | standard | elevated | critical
release_status: "development" # draft | development | staging | pre_release | production
backend_provider: "supabase" # supabase | firebase | custom_node | serverless | none
data_categories:
  - "credentials"
  - "personal_identifiable_info"
  - "financial_transactions"
  - "device_identifiers"
  - "analytics_telemetry"
active_features:
  auth: true
  payments: false
  ai_integration: false
  subscriptions: false
  social_login: false
  background_tasks: false
  push_notifications: false
  file_storage: false
third_party_services:
  - "supabase"
  - "sentry"
audit_modules:
  security: true
  privacy: true
  supply_chain: true
  legal_ip: true
  platform: true
  regional: true
  adversarial: true
  release: true
---

# Project Audit Scope Manifest

> **Manifest Purpose & Usage**:
> This document defines the formal audit boundaries and configuration for the application.
> The YAML frontmatter acts as a machine-readable declaration consumed by `.agents/skills/auditor/SKILL.md` to determine which modular audit skills are activated.
> The sections below establish human-readable definitions of product architecture, regulatory scope, data flows, and explicit out-of-scope boundaries.
> Initialize this file during **Phase 1 (Product Scope)** alongside `docs/PRD.md` and user stories. Update it whenever architectural or compliance boundaries change.

---

## 1. Product Summary

- **Application Purpose**: [High-level summary of what the application does, its core value proposition, and the business problem it solves.]
- **Application Type**: [e.g. Native mobile application, responsive Web/SaaS, REST/GraphQL API backend, browser extension, or cross-platform solution.]
- **Primary Tech Stack**:
  - Frontend: [e.g. React Native / Expo, Next.js, Vite React, Flutter]
  - Backend & Database: [e.g. Supabase (PostgreSQL, PostgREST, GoTrue Auth), Custom Node.js/Fastify, Firebase]
  - State Management & Network: [e.g. Zustand, React Query, Redux Toolkit]
- **Target Release Audience**: [e.g. B2C consumer users in Southeast Asia, B2B enterprise tenants globally, internal operations.]

---

## 2. Intended Users & Personas

| Persona Name | Authentication Type | Data Access Level | Privileges & Capabilities |
|---|---|---|---|
| **Anonymous Visitor** | Unauthenticated | Public catalog / marketing views only | Read-only access to public endpoints. No PII access. |
| **Standard User / Tenant** | Authenticated (Email OTP / OAuth) | Personal tenant row-level data only | Create/read/update/delete owned resources strictly scoped by `auth.uid()`. |
| **Elevated / Team Admin** | Authenticated + MFA | Organization / workspace tenant data | Manage workspace members, billing settings, and team-scoped data. |
| **System Administrator** | Authenticated + Hardware MFA / Service Role | Global operational diagnostics & support | Access restricted administrative tooling via isolated backchannel; audit-logged. |

- **Multi-Tenancy Isolation Principle**: [Specify tenancy model: Shared database with Row Level Security (RLS) tenant isolation vs database-per-tenant.]

---

## 3. Distribution & Deployment

- **Distribution Channels**:
  - Apple App Store (iOS): [Yes/No, bundle ID: e.g. `com.example.app`]
  - Google Play Store (Android): [Yes/No, package name: e.g. `com.example.app`]
  - Web Application / SaaS: [Yes/No, primary domain: e.g. `https://app.example.com`]
  - Direct Binary / APK: [Yes/No, internal enterprise distribution]
- **Deployment Environments**:
  - `development`: Local developer environment / mock backend.
  - `staging`: Pre-production environment for integration testing and QA.
  - `production`: Live customer-facing environment with strict release controls.
- **Artifact Signing & Key Management**:
  - iOS signing managed via [e.g. Fastlane Match, Xcode automatic signing, EAS Build credentials].
  - Android signing keystore stored in [e.g. secure cloud secret manager, EAS credentials, CI environment secret].

---

## 4. Data Inventory & Flow

| Data Category | Specific Data Elements | Collection Purpose | Storage Location | Retention / Deletion Policy |
|---|---|---|---|---|
| **Credentials & Auth** | Password hash, salt, session tokens, refresh tokens, OTP codes | Authentication & session management | Auth provider secure vault / encrypted database | Session expiry: 7 days. Permanent purge on account deletion. |
| **Personal Identifiable Info (PII)** | Full name, email address, phone number, avatar URL | User profile & transaction identification | Database table `users` / `profiles` with RLS | Soft deletion on user request; hard purge within 30 days. |
| **Financial & Transactional** | Order history, transaction ID, payment gateway token | Purchase fulfillment & tax compliance | Database table `transactions` (No raw PAN/CVV stored) | Retained for 5–7 years per financial record regulations. |
| **Device & Diagnostics** | Device model, OS version, app version, IP address, crash logs | Bug triaging & platform optimization | Sentry / PostHog / Supabase logs | Retained 90 days, IP addresses truncated / anonymized. |
| **Telemetry & Usage** | Screen views, feature taps, session duration | Product analytics & UX improvements | Analytics SDK (e.g. PostHog, Mixpanel) | Opt-out supported; data retained for 12 months. |

- **Data Flow Narrative**:
  1. Client captures user input over TLS 1.3 to backend API.
  2. Backend validates schemas (e.g. Zod), enforces RLS based on JWT context.
  3. Sensitive payment details are tokenized directly with licensed gateways (never pass through client/backend storage).
  4. Backups are encrypted at rest using AES-256.

---

## 5. External Services & Integrations

| Service Name | Provider / Domain | Purpose | Data Transmitted | Credentials & Boundary Handling |
|---|---|---|---|---|
| **Supabase** | `supabase.co` | Auth, PostgreSQL Database, Edge Functions, Storage | User credentials, application relational data | Client uses publishable anon key; RLS enforced server-side. Service role key strictly server-only. |
| **Sentry** | `sentry.io` | Crash reporting & error diagnostics | Stack traces, device metadata, breadcrumbs | DSN public key; PII scrubbers enabled to redact emails/passwords. |
| **Payment Gateway** | [e.g. Midtrans / Stripe] | Payment processing & tokenization | Amount, transaction ref, customer contact, token | Client uses public key; Webhook signature verified with secret key on backend. |
| **Push Notifications** | [e.g. Expo Push / FCM / APNs] | Transactional and status notifications | Device push token, notification payload text | Server-side bearer authentication; payload contains no sensitive PII. |

---

## 6. Known Regulatory & Compliance Areas

- **Indonesian Jurisdictional Framework**:
  - **UU PDP (UU No. 27/2022 tentang Pelindungan Data Pribadi)**:
    - Explicit, recorded consent before personal data processing (Pasal 20–22).
    - User right to rectify and completely erase personal data (Pasal 8).
    - 72-hour mandatory breach notification readiness to authorities and users (Pasal 46).
    - Strict handling and parental consent for children's data (Pasal 25).
  - **PSE Kominfo (Permenkominfo 5/2020 & 10/2021)**:
    - Formal registration with Kominfo as a Private Scope Electronic System Operator (Penyelenggara Sistem Elektronik Lingkup Privat).
    - Maintenance of local contact point and grievance mechanism.
  - **Bank Indonesia / QRIS Regulations**:
    - Currency denominate in Indonesian Rupiah (IDR per UU No. 7/2011).
    - QRIS compliance (PADG No. 21/18/PADG/2019) via licensed Payment Service Provider (PJP).
- **Global Data Protection Frameworks**:
  - **GDPR (Regulation (EU) 2016/679)**: Lawful basis for processing, data portability, Article 17 right to erasure.
  - **COPPA (16 CFR Part 312)**: Child privacy protections if age-gate permits users under 13.
- **Mobile Store Review Policies**:
  - **Apple App Store Review Guidelines**: Guideline 4.8 (Sign in with Apple), Guideline 3.1.1 (In-App Purchases for digital goods), Guideline 5.1.1 (Privacy Manifest `PrivacyInfo.xcprivacy` and required reason APIs), mandatory Account Deletion in-app.
  - **Google Play Developer Program Policies**: Target SDK compliance, Google Data Safety declaration fidelity, prominent disclosures for runtime permissions.

---

## 7. Explicit Exclusions & Out-of-Scope Items

The following areas are explicitly excluded from the assurance audit for this project:

1. **Hardware-Level Security**: Physical tampering, side-channel electromagnetic attacks, hardware debugger attacks on compromised jailbroken devices.
2. **Third-Party Upstream Infrastructure**: Underlying cloud data center physical security (AWS, Google Cloud, Supabase host data centers) beyond provider-documented SLAs and compliance certifications.
3. **Carrier / Telecom Network Vulnerabilities**: SS7/Diameter attacks or cellular ISP eavesdropping outside TLS trust boundaries.
4. **Third-Party Payment Processor Internals**: PCI-DSS compliance of the external payment gateway core engine (responsibility rests with licensed payment gateway).
5. **Definitive Legal Counsel**: Audit findings provide engineering assurance observations, risk flags, and technical compliance evidence; they do not constitute formal legal representation or binding statutory certification.
