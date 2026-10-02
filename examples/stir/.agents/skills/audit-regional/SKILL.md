---
name: audit-regional
description: Audit jurisdiction-specific compliance including Indonesian PDP Law, PSE Kominfo, and local payments alongside global frameworks.
---

# Regional Regulatory Skill — Jurisdictional Compliance & Discovery

## Objective
Audit regional regulatory alignment based on [`docs/audit/AUDIT_SCOPE.md`](../../../docs/audit/AUDIT_SCOPE.md) target regions. Never assert that an application is "legal worldwide" or free from statutory liability.

---

## 1. Indonesian Regulatory Framework (ID Scope)

1. **UU No. 27/2022 tentang Pelindungan Data Pribadi (UU PDP)**:
   - *Consent (Pasal 20–22)*: Explicit, informed, opt-in consent before processing personal data.
   - *Rights of Data Subjects (Pasal 8)*: User right to access, rectify, and delete personal data.
   - *Breach Notification (Pasal 46)*: Architecture readiness for 72-hour written breach notice.
   - *Child Data (Pasal 25)*: Parental or guardian consent for data processing of minors.
2. **PSE Lingkup Privat Kominfo Registration**:
   - Verification of registration readiness under Permenkominfo No. 5/2020 & 10/2021 for electronic system operators.
   - Presence of local customer complaint and dispute resolution channel.
3. **Payment & QRIS Tokenization**:
   - Use licensed payment gateways (Midtrans, Xendit); never store raw PAN/CVV.
   - Transactions in Indonesian territory must display and transact in Rupiah (IDR) per UU Mata Uang.

---

## 2. Global Frameworks (GDPR & COPPA)

- **GDPR**: Verify explicit consent, lawful basis of processing, and data erasure mechanics (Article 17).
- **COPPA**: Enforce age verification and parental consent if application is directed to children under 13.

---

## 3. CLI Inspection Recipes

```bash
# Check privacy consent and terms acceptance mechanics
grep -rnEI "(privacy_policy|terms_of_service|consent|agree.*terms)" src/ app/ 2>/dev/null

# Check local payment gateway integrations and currency
grep -rnEI "(midtrans|xendit|qris|currency.*IDR)" src/ app/ 2>/dev/null

# Check user deletion or data purge functions
grep -rnEI "(anonymize|purgeUser|deleteUserData)" src/ app/ 2>/dev/null
```

---

## 4. Output & Classification

Record regional compliance findings in `docs/audit/findings/` using status `BLOCK`, `WARN`, `REVIEW`, or `PASS`.
