# US-001: [Feature Title]

## Changelog
- **YYYY-MM-DD**: Created user story.

| Field | Value |
|---|---|
| **Story ID** | US-001 |
| **Title** | [Feature Title] |
| **Module** | [Core / Auth / Dashboard / etc.] |
| **Screen / Route** | [e.g. app/(tabs)/dashboard.tsx] |
| **Priority** | [High / Medium / Low] |
| **Status** | [Draft / Ready / Implemented / Verified] |

---

## 👤 User Story

**As a** [type of user persona],  
**I want to** [perform a specific action],  
**So that** [achieve a tangible benefit or goal].

---

## 🎯 Acceptance Criteria

### Scenario 1: Happy Path
- **Given** [precondition or initial state]
- **When** [user performs valid action]
- **Then** [expected success state or data update occurs]
- **And** [visual feedback is presented]

### Scenario 2: Validation Failure
- **Given** [user is on the form or action view]
- **When** [user submits empty or invalid input]
- **Then** [system blocks submission]
- **And** [clear validation message is displayed]

### Scenario 3: Flow Cancellation
- **Given** [user has begun editing]
- **When** [user taps cancel / dismiss]
- **Then** [modal or form closes without saving changes]

---

## 🧭 Associated Flow

See [`docs/flows/flow-template.md`](../flows/flow-template.md) for navigation routing and entry/exit specifications.

---

## 🧪 Test & Verification Matrix

| Test Case | Scenario | Input / Action | Expected Outcome | Verification Status |
|---|---|---|---|---|
| **TC-01** | Happy Path | Valid input submitted | Data saved; success toast shown | Untested |
| **TC-02** | Validation | Empty required field | Error shown; submit blocked | Untested |
| **TC-03** | Cancel | Press Batal/Cancel | Returns to parent with no change | Untested |
