# Feature Flow: [Feature Name]

## Changelog
- **YYYY-MM-DD**: Documented initial flow.

## Purpose
Describe the user goal and end-to-end journey represented by this flow.

## Related User Stories
- [`US-001`](../user-stories/US-001-template.md)

---

## 🗺️ Mermaid Flowchart

```mermaid
flowchart TD
    Start[User on Dashboard] --> Action[Tap CTA / Action Button]
    Action --> View[Open Feature Modal / Screen]
    View --> Input[Fill Form Inputs]
    Input --> Validate{Valid Input?}
    Validate -->|No| Error[Show Field Error Banner]
    Error --> Input
    Validate -->|Yes| Submit[Submit Action]
    Submit --> Success[Show Feedback / Toast]
    Success --> Return[Return to Dashboard with Refreshed State]

    View --> Cancel[Tap Cancel / Backdrop]
    Cancel --> Return
```

---

## 🔍 Reachability & Discovery Contract

- **Discovery**: How does the user discover this feature? (e.g. Floating action button on Dashboard)
- **Primary Entry Point**: Dashboard screen CTA.
- **Direct-URL Policy**: Feature must be reachable via normal UI navigation. Direct URL access alone does not satisfy integration criteria.
- **Cancellation**: Backdrop tap or explicit cancel dismisses the view without saving.
- **Return Destination**: Returns user to the originating screen with updated state.
