---
name: audit-supply-chain
description: Audit direct and transitive dependencies, lockfile integrity, known vulnerabilities, install scripts, and package health.
---

# Supply Chain Audit Skill — Dependency Hygiene & Integrity

## Objective
Detect vulnerable dependencies, malicious install scripts, and lockfile synchronization drift using native package tools. Never permit unpinned or critically vulnerable packages into production builds.

---

## 1. Audit Checkpoints

1. **Lockfile Synchronization & Integrity**:
   - Verify lockfile (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `Podfile.lock`) exists, is committed, and is synchronized with manifest files.
2. **Vulnerability Assessment**:
   - Audit production dependencies for known CVEs. Critical and high vulnerabilities block release.
3. **Install Lifecycle Scripts**:
   - Inspect `preinstall`, `install`, and `postinstall` hooks in direct and transitive dependencies for suspicious commands or remote payload execution.
4. **Package Health & Abandonment**:
   - Check for unmaintained, deprecated, or single-maintainer abandoned dependencies (>2 years without updates).
5. **Software Bill of Materials (SBOM)**:
   - Verify full dependency tree can be enumerated deterministically.

---

## 2. CLI Inspection Recipes

```bash
# Native package vulnerability audit (production dependencies)
npm audit --production

# Detect lifecycle scripts in dependencies
grep -rnEI '"(postinstall|preinstall|install)":' package.json package-lock.json node_modules/*/package.json 2>/dev/null

# Outdated package detection
npm outdated

# Check lockfile presence and modification status
git status --porcelain "*lock*"
```

---

## 3. Output & Classification

Record supply chain vulnerabilities in `docs/audit/findings/` using status `BLOCK`, `WARN`, `REVIEW`, or `PASS`.
