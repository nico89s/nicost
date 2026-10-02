---
name: audit-release
description: Enforce pre-release distribution gates by auditing production artifacts, debug flags, staging URLs, test credentials, and signing.
---

# Release Audit Skill — Distribution Gate & Artifact Verification

## Objective
Verify final distribution packages and production configurations before store submission or deployment. Inspect actual built production artifacts whenever available rather than source assumptions.

---

## 1. Distribution Gate Checkpoints

1. **Debug Flags & Log Suppression**:
   - Confirm `__DEV__` is disabled, source maps are omitted or restricted, and verbose `console.log` statements are stripped in release builds.
2. **Staging Endpoints & Sandbox Leakage**:
   - Ensure no `localhost`, `127.0.0.1`, staging domains, or mock API hosts remain in production configs.
3. **Test Keys & Mock Credentials**:
   - Verify zero sandbox API keys (e.g. `pk_test_`, `sk_test_`, `SB-Mid-server-`) exist in production bundles.
4. **Signing, Versioning & Artifact Integrity**:
   - Verify production release signing keys (keystore, provisioning profiles) and version bump consistency (`version`, `buildNumber`).
5. **Store Metadata & Mandatory Legal Links**:
   - Confirm presence of valid Privacy Policy URL, Terms of Service URL, and required support contacts.

---

## 2. Zero-Install CLI Inspection Recipes

```bash
# Localhost and staging URL scan in production configs
grep -rnEI "(localhost|127\.0\.0\.1|staging\.|sandbox\.)" --exclude-dir={node_modules,.git,test,tests} . 2>/dev/null

# Test API keys and sandbox tokens
grep -rnEI "(pk_test_|sk_test_|SB-Mid-server-)" --exclude-dir={node_modules,.git} .

# Inspect build configs for minification and debug flags
grep -rnEI "(minify|drop_console|sourceMap)" *config* package.json 2>/dev/null
```

---

## 3. Release Gate Verdict

Output a release gate verdict in `docs/audit/AUDIT_REPORT.md`: `GO` (all clear), `CONDITIONAL GO` (minor non-blocking items), or `NO-GO` (blocking release defect).
