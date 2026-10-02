---
name: audit-security
description: Audit authentication, authorization/RLS, secrets hygiene, storage, network, and abuse limits based on OWASP MASVS and web security baselines.
---

# Security Audit Skill — Baseline Protection & Access Control

## Objective
Verify technical security controls against [`docs/SECURITY_MODEL.md`](../../../docs/SECURITY_MODEL.md) and OWASP MASVS L1 baselines. Never assume client-side checks or unverified tokens protect backend resources.

---

## 1. Audit Checkpoints

1. **Authentication & Session Tokens**:
   - Verify session tokens are stored in secure platform storage (iOS Keychain, Android EncryptedSharedPreferences) rather than unencrypted `localStorage` or `AsyncStorage`.
   - Confirm token expiry, refresh rotation, and server-side revocation paths.
2. **Authorization & Row Level Security (RLS)**:
   - Verify every database table enforces RLS with tenant isolation (`auth.uid() = user_id`).
   - Confirm server-side functions / RPCs explicitly validate caller identity and role.
3. **Secrets Hygiene**:
   - Ensure zero private keys, API secrets, or database service credentials exist in client source code or git history.
4. **Data Storage & Transport Security**:
   - Verify all network communication enforces TLS/HTTPS; flag cleartext HTTP endpoints.
   - Ensure sensitive personal or financial data is encrypted at rest.
5. **Abuse Mitigation & Rate Limiting**:
   - Ensure rate limits and brute-force protections exist on login, registration, and OTP endpoints.

---

## 2. Zero-Install CLI Inspection Recipes

```bash
# Secrets and private credentials scan
grep -rnEI "(api[_-]?key|secret|service_role|bearer|private[_-]?key)" --exclude-dir={node_modules,.git,dist,build} .

# Check git commit history for leaked credentials
git log -p -S "service_role" -n 5

# Insecure storage calls in client code
grep -rnEI "(localStorage\.setItem|AsyncStorage\.setItem)" --exclude-dir={node_modules,.git} .

# Cleartext HTTP URLs
grep -rnEI "http://(?!localhost|127\.0\.0\.1)" --exclude-dir={node_modules,.git} .
```

---

## 3. Output & Classification

Record discovered issues in `docs/audit/findings/` using status `BLOCK`, `WARN`, `REVIEW`, or `PASS`.
