---
name: audit-privacy
description: Construct data maps, audit Apple Privacy Manifests and Google Data Safety, and reconcile actual code behavior against privacy disclosures.
---

# Privacy Audit Skill — Data Mapping & Disclosure Reconciliation

## Objective
Reconcile actual codebase data practices against declared privacy manifests and store disclosures. Never allow undeclared telemetry, identifier collection, or tracking SDKs to ship.

---

## 1. Audit Checkpoints

1. **Data Inventory & Mapping**:
   - Catalog all user data collected (PII, credentials, device identifiers, analytics, telemetry).
   - Trace data lifecycle: collection points, storage locations, third-party transmission, retention.
2. **Apple Privacy Manifest (`PrivacyInfo.xcprivacy`)**:
   - Verify `PrivacyInfo.xcprivacy` declares all Required Reason APIs used (e.g. `UserDefaults`, `systemUptime`, `statFs`, `NSFileModificationDate`).
   - Confirm declared tracking domains (`NSPrivacyTrackingDomains`) and data types match code behavior.
3. **Google Play Data Safety Reconciliation**:
   - Reconcile collected/shared data fields against Google Play Data Safety form disclosures.
   - Verify purpose declarations (app functionality, analytics) and encryption in transit.
4. **Code vs. Disclosure Reconciliation**:
   - Compare third-party SDK calls against privacy disclosures to expose undeclared tracking.
   - Check console logs, error reporters, and telemetry to verify no PII is logged.

---

## 2. Zero-Install CLI Inspection Recipes

```bash
# iOS Required-Reason API detection
grep -rnEI "(UserDefaults|statFs|systemUptime|NSFileModificationDate)" --exclude-dir={node_modules,.git} .

# Analytics, telemetry, and tracking SDK imports
grep -rnEI "(analytics|mixpanel|amplitude|firebase|sentry|datadog)" --exclude-dir={node_modules,.git} .

# Device identifier harvesting
grep -rnEI "(getDeviceId|advertisingId|getMacAddress|IDFV|IDFA)" --exclude-dir={node_modules,.git} .
```

---

## 3. Output & Classification

Record discrepancies in `docs/audit/findings/` using status `BLOCK`, `WARN`, `REVIEW`, or `PASS`.
