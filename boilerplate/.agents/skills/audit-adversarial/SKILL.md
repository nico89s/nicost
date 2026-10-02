---
name: audit-adversarial
description: "Challenge trust boundaries with attacker mindset: test entitlement bypasses, IDOR, parameter tampering, and prompt injection."
---

# Adversarial Audit Skill — Threat Simulation & Boundary Probing

## Objective
Probe trust boundaries defined in [`docs/SECURITY_MODEL.md`](../../../docs/SECURITY_MODEL.md) adopting an attacker mindset. Assume the client is fully compromised, untrusted, and reverse-engineered.

---

## 1. Adversarial Attack Vectors

1. **Entitlement Bypass (Free-to-Pro Forgery)**:
   - Probe if premium features rely solely on client-side state (`isPro`, `hasSubscription`) or if backend APIs strictly validate server-side receipts and entitlements.
2. **Insecure Direct Object Reference (IDOR)**:
   - Verify API endpoints and RPCs validate that the requesting user owns the target entity UUID, preventing cross-tenant data exfiltration.
3. **Parameter Tampering & Price Manipulation**:
   - Check if clients can manipulate prices, discounts, quantities, or currency fields sent to backend APIs.
4. **API Abuse & Unbounded Queries**:
   - Check for missing pagination limits, lack of request payload size bounds, or exposed internal debug endpoints.
5. **Prompt Injection & LLM Jailbreaks**:
   - If AI models are integrated: test whether untrusted user inputs can bypass system instructions, leak internal context, or hijack tool invocation.

---

## 2. Zero-Install CLI Inspection Recipes

```bash
# Find client-side entitlement state variables
grep -rnEI "(isPro|isPremium|hasAccess|isSubscribed|userTier)" src/ app/ 2>/dev/null

# Check queries potentially missing tenant filters
grep -rnEI "WHERE id\s*=" src/ app/ supabase/ 2>/dev/null

# Inspect LLM prompt concatenation for unescaped user inputs
grep -rnEI "(prompt|messages).*\$\{.*input.*\}" src/ app/ 2>/dev/null
```

---

## 3. Output & Proof-of-Concept

Document reproducible attack vectors in `docs/audit/findings/` using status `BLOCK`, `WARN`, or `REVIEW`.
