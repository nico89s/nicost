# System Security Model & Threat Model

> **Relationship to ARCHITECTURE.md**:
> SECURITY_MODEL.md documents the INTENDED security design (threat model, trust boundaries, actor definitions). ARCHITECTURE.md §Security documents the IMPLEMENTED security controls (RLS policies, RPCs, credential configuration). The Auditor compares intent against reality to find gaps.

---

## 1. Changelog

| Version | Date | Author / Agent | Summary of Changes |
|---|---|---|---|
| `1.0.0` | [YYYY-MM-DD] | [Security Lead / Agent] | Initial system security model, threat actor definitions, and trust boundaries. |

---

## 2. Security Objectives

The intended security architecture enforces five primary objectives across all target platforms:

1. **Confidentiality & Tenant Isolation**: Sensitive user data, authentication tokens, and private records must only be accessible to verified owners (`auth.uid() = user_id`) or authorized tenant administrators.
2. **Integrity & Anti-Tampering**: Critical state transitions—including financial transactions, user entitlements, and access control roles—must be calculated and enforced authoritatively on the backend. Clients are never trusted for entitlement or billing logic.
3. **Availability & Resilience**: The system must resist abuse, brute-force attacks, resource exhaustion, and Denial-of-Service through aggressive rate limiting, input size bounds, and query pagination.
4. **Accountability & Auditability**: All privileged operations, administrative actions, and state mutations must generate verifiable, timestamped audit log records.
5. **Regulatory Alignment**: Technical controls must comply with regional data protection mandates (e.g. Indonesia UU No. 27/2022 PDP, EU GDPR) and mobile app store review guidelines (Apple, Google).

---

## 3. Assets & Protected Resources

The following assets are classified by criticality and require strict isolation controls:

| Asset Classification | Asset Description | Target Protection Level | Primary Controls |
|---|---|---|---|
| **Tier 1: Critical Secrets** | Database root credentials, `service_role` keys, payment private API keys, webhook signing secrets | Zero exposure outside secure server runtimes | Environment variable injection, secret manager, zero git presence |
| **Tier 2: User Auth & Identity** | Password hashes, session tokens, refresh tokens, OTP codes | High: Protected at rest and in transit | Secure enclave / Keychain storage, bcrypt/argon2 hashing, HTTPS only |
| **Tier 3: Financial & Transactions**| Transaction history, payment tokens, invoice records, balance states | High: Server-authoritative integrity | Backend-only calculations, idempotency keys, immutable audit ledger |
| **Tier 4: Personal Data (PII)** | Real names, email addresses, phone numbers, location data | Medium-High: Strict user-scoped isolation | Database Row Level Security (RLS), field encryption, deletion endpoints |
| **Tier 5: Entitlements & Features** | Premium status (`isPro`), tier subscription flags, quota limits | High: Server-enforced access gates | Backend token/JWT claims or DB verification; never client-authoritative |

---

## 4. Threat Actors & Attack Vectors

### Threat Actors
- **TA-1: Unauthenticated External Adversary**: Remote internet attacker scanning for exposed APIs, unauthenticated RPCs, leaked keys in client bundles, or brute-forcing authentication endpoints.
- **TA-2: Authenticated Malicious Tenant**: Legitimate platform user attempting to access or modify other users' data (Insecure Direct Object Reference / IDOR) or forge administrative claims.
- **TA-3: Compromised Client Device / Local Attacker**: Attacker with physical control over a rooted/jailbroken mobile device or browser runtime, using proxies, memory modifiers, or debuggers to bypass local gates.
- **TA-4: Malicious Supply-Chain Dependency**: Compromised npm/pip package executing malicious `postinstall` lifecycle scripts or harvesting runtime environment variables.
- **TA-5: Compromised Operator / Insider**: Rogue or compromised internal staff member attempting unauthorized data extraction or tampering.

### Attack Vectors & Mitigations
- **AV-1: Client-Side Entitlement Forgery (Free-to-Pro)**:
  - *Vector*: User tampers with local storage (e.g. AsyncStorage `isPro: true`) or patches client JS code.
  - *Mitigation*: Entitlements are validated server-side on every protected API call or RPC execution.
- **AV-2: Insecure Direct Object Reference (IDOR)**:
  - *Vector*: Malicious tenant alters UUID parameters in REST/RPC calls to access another tenant's records.
  - *Mitigation*: Row Level Security (RLS) strictly enforces `auth.uid() = user_id` at the database engine level.
- **AV-3: Financial Parameter Tampering**:
  - *Vector*: User sends altered price, negative quantities, or forged discount codes in checkout payload.
  - *Mitigation*: Backend recalculates all prices and quantities from canonical product catalog; client payload amounts are discarded.
- **AV-4: Leaked Administrative Credentials**:
  - *Vector*: Accidental commit of `service_role` or payment private key to git or client bundle.
  - *Mitigation*: Automated secret scanning in CI; strict segregation of public client keys from private server keys.
- **AV-5: LLM / AI Prompt Injection**:
  - *Vector*: User supplies malicious prompt input designed to hijack AI tool execution or leak system instructions.
  - *Mitigation*: Strict delimiter separation, input sanitization, and read-only tool scoping.

---

## 5. Trust Boundaries

```
[ Compromised / Untrusted Device ]
       │  (Public Internet / HTTPS)
───────▼─────────────────────────────────────────────────── Trust Boundary 1: Client vs API Gateway
[ Edge Functions / API Gateway / Reverse Proxy ]
       │  (Private Cloud Network / Authenticated JWT Context)
───────▼─────────────────────────────────────────────────── Trust Boundary 2: Tenant Isolation (RLS)
[ PostgreSQL Database with Row Level Security ]
       │  (Encrypted Internal VPC)
───────▼─────────────────────────────────────────────────── Trust Boundary 3: External Services & Webhooks
[ Payment Gateways (Midtrans/Stripe) | Push Services (FCM/APNs) | Sentry ]
       │  (MFA + Dedicated IP Whitelist)
───────▼─────────────────────────────────────────────────── Trust Boundary 4: Administrative Backchannel
[ Internal Admin Tooling / Service Role Operations ]
```

- **Boundary 1 (Client ↔ Backend)**: The client application running on user hardware is completely **untrusted**. All inputs, parameters, headers, and query strings must be validated and sanitized.
- **Boundary 2 (Tenant A ↔ Tenant B)**: Multi-tenant database boundary. No tenant may read or write records belonging to another tenant unless explicit sharing rules exist.
- **Boundary 3 (Backend ↔ External Services)**: External webhook calls are untrusted until cryptographic signatures (HMAC SHA-256) and replay timestamps are verified.
- **Boundary 4 (Standard User ↔ System Admin)**: Administrative operations are isolated from consumer endpoints and require elevated authentication.

---

## 6. Authentication Rules

1. **Identity Verification**:
   - Primary methods: Passwordless Email OTP, standard OAuth 2.0 / OpenID Connect, or Sign in with Apple (mandatory on iOS if third-party social auth is offered).
   - Passwords (if implemented) must require a minimum of 8 characters and be hashed using bcrypt or Argon2 with cryptographic salts.
2. **Session & Token Management**:
   - Access tokens (JWT) must be short-lived (maximum lifetime: 1 hour).
   - Refresh tokens must be stored securely:
     - **Mobile (iOS/Android)**: Stored strictly in Keychain (iOS) and EncryptedSharedPreferences / Keystore (Android). Never in unencrypted `AsyncStorage`.
     - **Web**: Stored in `httpOnly`, `Secure`, `SameSite=Strict` cookies or in-memory state. Never in unencrypted `localStorage` if vulnerable to XSS.
3. **Session Revocation & Logout**:
   - User logout must invalidate the server-side refresh token session, not merely clear client-side state.
   - Account deletion must immediately revoke all active sessions across all devices.

---

## 7. Authorization Model & User Ownership

1. **Row-Level Ownership Invariant**:
   - Every database table containing tenant-scoped data **must** include an immutable owner identifier column: `user_id uuid NOT NULL REFERENCES auth.users(id)`.
2. **Database Engine Enforcement (RLS)**:
   - Row Level Security (RLS) must be enabled (`ALTER TABLE <table_name> ENABLE ROW LEVEL SECURITY;`) on **100%** of public tables.
   - Policies must explicitly bind data access to the authenticated user context:
     ```sql
     -- Select Policy:
     CREATE POLICY "Users can only read own data" ON <table_name>
       FOR SELECT USING (auth.uid() = user_id);

     -- Insert Policy:
     CREATE POLICY "Users can only insert own data" ON <table_name>
       FOR INSERT WITH CHECK (auth.uid() = user_id);

     -- Update Policy:
     CREATE POLICY "Users can only update own data" ON <table_name>
       FOR UPDATE USING (auth.uid() = user_id) WITH CHECK (auth.uid() = user_id);

     -- Delete Policy:
     CREATE POLICY "Users can only delete own data" ON <table_name>
       FOR DELETE USING (auth.uid() = user_id);
     ```
3. **Server-Side Authorization Guarantee**:
   - Handlers and RPCs must derive identity from `auth.uid()` extracted from the verified JWT, never from user-controllable request body parameters.

---

## 8. Roles & Privileged Operations

### Role Hierarchy
- `anon`: Unauthenticated public visitor. Permitted read access only to public catalog or configuration tables.
- `authenticated`: Standard verified user. Permitted read/write access strictly to resources where `user_id = auth.uid()`.
- `tenant_admin`: Elevated user within an organization/team. Permitted management of organization members and team resources.
- `service_role`: Privileged administrative execution context. Bypasses RLS; restricted strictly to server-side Edge Functions, background workers, and administrative scripts.

### Privileged Operations Safeguards
- **Sensitive Operations**: Password resets, email changes, billing subscription modifications, account deletions, and role assignments are classified as sensitive.
- **Re-Authentication**: Sensitive account mutations require re-authentication (password confirmation or fresh OTP) prior to execution.
- **Service Role Isolation**: The `service_role` API key must never be bundled into mobile binaries, web client source, or public-facing edge functions.

---

## 9. Backend & Database Boundaries

1. **Direct Connection Prohibition**:
   - Client applications must never connect directly to raw PostgreSQL TCP ports (5432/6543). All communication occurs over HTTPS through PostgREST, GraphQL, or Edge Functions.
2. **Stored Procedures & RPC Policies**:
   - Stored functions declared with `SECURITY DEFINER` run with the privileges of the function owner. They must:
     - Explicitly define `SET search_path = public, pg_temp;` to prevent search path hijacking.
     - Validate caller identity using `auth.uid()` or explicitly require `service_role`.
     - Validate and sanitize all input arguments before executing SQL statements.
3. **Schema Isolation**:
   - System internals, payment gateway raw logs, and administrative tables must reside in private schemas (e.g. `private.*`) not exposed to PostgREST.

---

## 10. External Services & Integrations

1. **Inbound Webhook Verification**:
   - All inbound webhooks (e.g. Midtrans, Stripe payment notifications) must verify cryptographic signatures (HMAC SHA-256) against the shared webhook secret before processing.
   - Replay protection: Webhooks must validate event timestamps and maintain an idempotent processed-event ledger.
2. **Outbound API Integration**:
   - Outbound requests to third-party APIs (payment processors, push providers, AI endpoints) must use scoped, least-privilege API credentials.
   - Outbound requests must enforce strict timeouts (maximum 10 seconds) and retry with exponential backoff.
3. **Data Minimization on Egress**:
   - Only strictly required data fields are transmitted to external services.
   - Sentry and analytics SDKs must enable automated PII scrubbing (redacting passwords, tokens, full credit card numbers, and national IDs).

---

## 11. Secrets Management

### Secret Classification
| Secret Type | Examples | Allowed Distribution | Storage & Injection Method |
|---|---|---|---|
| **Public / Publishable** | Supabase Anon Key, Sentry DSN, Stripe Publishable Key | Client application bundles, mobile binaries | Injected via build-time environment (`EXPO_PUBLIC_*`, `NEXT_PUBLIC_*`) |
| **Private / Server-Only** | Supabase Service Role Key, Database connection URI, Stripe Secret Key, Midtrans Server Key | Edge Functions, backend server runtimes only | Injected via runtime environment variables; NEVER in client bundles |

### Hygiene & Governance Rules
1. **Zero Git Presence**: No secret or credential may ever be committed to git repositories or `.env` files tracked by version control.
2. **Repository Screening**: Pre-commit hooks and CI scans (`git-secrets` / `trufflehog` / regex grep) must block credential commits.
3. **Revocation & Rotation**: Any secret accidentally exposed in client code or git history must be treated as immediately compromised and rotated within 2 hours.

---

## 12. Payment & Financial Boundaries

1. **Zero Cardholder Data Storage**:
   - The application and database must **never** receive, process, or store raw Primary Account Numbers (PAN), CVVs, or card expiration dates.
   - Payment input fields must use secure iframes or SDK elements provided by licensed payment gateways.
2. **Server-Authoritative Pricing**:
   - Order totals, item prices, tax calculations, and discount deductions must be authoritatively calculated by backend code.
   - Any price, discount, or total amount submitted by the client must be discarded.
3. **Idempotent Transaction Processing**:
   - All payment creation and fulfillment operations must accept and enforce unique idempotency keys (`idempotency_key uuid`) to prevent double-charging on network retries.
4. **Local Currency & Payment Regulation (Indonesia)**:
   - Transactions within Indonesia must settle in Indonesian Rupiah (IDR per UU No. 7/2011).
   - QRIS payments must adhere to Bank Indonesia standard QR specifications and route through a licensed Payment Service Provider (PJP).

---

## 13. Administrative Boundaries

1. **Portal & Route Isolation**:
   - Administrative dashboards must reside on separate subdomains or isolated network boundaries, not within the public consumer mobile app.
2. **Multi-Factor Authentication (MFA)**:
   - All administrative accounts must require mandatory MFA (TOTP or hardware security key) to access administrative operations.
3. **Immutable Audit Logging**:
   - Privileged operations (user banning, tenant quota modification, manual financial adjustments) must write an immutable audit log entry containing:
     - `timestamp` (UTC ISO string)
     - `admin_id` (Actor UUID)
     - `target_resource` (Table / Entity UUID)
     - `action_type` (CREATE, UPDATE, DELETE, OVERRIDE)
     - `diff_summary` (Old state vs new state)
     - `origin_ip` and `user_agent`

---

## 14. Threat Assumptions & Known Trade-Offs

### Threat Assumptions
- **Hostile Client Environment**: Any code executing on a user's mobile device or browser is assumed to be fully readable, reverse-engineerable, and modifiable by an adversary.
- **Untrusted Network**: All networks (including cellular and Wi-Fi) are assumed to be monitored or subject to Man-in-the-Middle (MitM) attempts; all communication must terminate TLS 1.3.
- **Cloud Infrastructure Reliability**: Underlying hypervisors, VPC infrastructure, and managed databases (AWS, Google Cloud, Supabase) are assumed to maintain physical and virtualization security according to SOC 2 Type II / ISO 27001 certifications.

### Known Architectural Trade-Offs
- **Offline Mode vs Immediate RLS Verification**: Local offline optimistic updates in mobile apps provide responsive UX but cannot enforce immediate backend RLS until synchronization occurs. Any local state that fails synchronization is discarded.
- **Publishable Anon Key in Client**: The client bundle includes the Supabase Anon Key. This trade-off is secure *only* because the database enforces 100% RLS policy coverage.
- **Third-Party Crash Diagnostics**: Sentry SDK integration transmits crash stack traces to third-party servers. This trade-off is accepted to enable rapid defect triage, mitigated by client-side PII scrubbing.
