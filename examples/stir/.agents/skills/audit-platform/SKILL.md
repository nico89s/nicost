---
name: audit-platform
description: Audit iOS App Store and Google Play compliance against official store review guidelines at audit time.
---

# Platform Compliance Skill — Store Guidelines & Review Readiness

## Objective
Verify mobile applications against Apple App Review Guidelines and Google Play Developer Program Policies to prevent store rejection. Evaluate against current official requirements at audit time, not stale assumptions.

---

## 1. Store Review Checkpoints

1. **Sign in with Apple (Guideline 4.8)**:
   - Mandatory if third-party social logins (Google, Facebook, etc.) are implemented.
2. **In-App Purchases vs External Paywalls (Guideline 3.1.1)**:
   - Digital goods, premium unlocks, and subscriptions must route through StoreKit / Google Play Billing. Never provide external purchase links.
3. **In-App Account Deletion (Apple 5.1.1(v) & Google Play)**:
   - If the app supports account creation, it MUST provide in-app initiation of account and associated data deletion.
4. **Permission Justifications & Minimization**:
   - Verify `Info.plist` usage descriptions (`NSCameraUsageDescription`, `NSLocationWhenInUseUsageDescription`) provide explicit, user-facing rationale.
   - Verify Android permissions in `AndroidManifest.xml` are minimal and justified.
5. **Target SDK & Store Metadata**:
   - Verify Android `targetSdkVersion` meets Google Play requirements for the current release year.
   - Confirm privacy policy URL is valid and publicly reachable.
6. **Apple Privacy Manifest (`PrivacyInfo.xcprivacy`)**:
   - Verify inclusion in target bundle if using Required Reason APIs or third-party tracking domains.
7. **Google Play Data Safety & Web Deletion URL**:
   - Verify Play Console Data Safety form submission matches app behavior, and confirm presence of a public web URL for account and data deletion.

---

## 2. Zero-Install CLI Inspection Recipes

```bash
# Check iOS permission description strings
grep -rnEI "(NSCameraUsageDescription|NSLocation.*Description|NSPhotoLibrary.*Description)" ios/ app.json 2>/dev/null

# Check Android declared permissions
grep -rnEI "(uses-permission|ACCESS_FINE_LOCATION|CAMERA)" android/ app.json 2>/dev/null

# Verify in-app account deletion flow presence
grep -rnEI "(deleteUser|deleteAccount|delete_account|removeAccount)" src/ app/ 2>/dev/null
```

---

## 3. Output & Classification

Record store compliance blockers in `docs/audit/findings/` using status `BLOCK`, `WARN`, `REVIEW`, or `PASS`.
