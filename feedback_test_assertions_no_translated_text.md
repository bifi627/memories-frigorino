---
name: feedback_test_assertions_no_translated_text
description: "UI uses t()/translations; Reqnroll/Playwright test handlers assert on testids or data-* attributes, never on translated text content"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 45954d69-08d8-41e2-a35d-ea4e684ff6d7
---

Test handlers (Reqnroll step definitions, Playwright assertions) must NOT assert on translated user-facing text. They assert on stable test attributes — `data-testid`, `data-role`, or other `data-*` attributes that the component renders alongside the translated text.

**Why:** Translations are loaded asynchronously by `i18next-http-backend` (loadPath `/locales/{{lng}}/translation.json`). In Playwright runs the chip text can resolve to either the translated value, the i18n key path, or empty during the load window. This caused 3 of 27 integration tests to flake until the assertion was switched to a stable attribute. See `Application/Frigorino.IntegrationTests/Slices/Households/Members/MemberSteps.cs` — the role assertion uses `ToHaveAttributeAsync("data-role", expectedRole)`, not `ToHaveTextAsync(expectedRole)`.

**How to apply:**
- UI components: keep using `t()` / translation hooks for any user-facing string. Don't hardcode English just to make tests pass.
- When a new UI element will be asserted on by a Reqnroll step, render a sibling attribute carrying the canonical English / stable identifier (e.g. `data-role={roleNames[role]}` in `MemberListItem.tsx`). Keep a constant `roleNames`-style map next to the translation hook so the canonical form has one source of truth.
- Step definitions: prefer `GetByTestId(...)` + `ToHaveAttributeAsync(...)` / `ToBeVisibleAsync()` / `ToHaveCountAsync(...)`. Reserve text assertions for cases where the *content itself* (e.g. user-typed input echoed back) is the thing under test.
- Don't "fix" a flaky test by reverting i18n in the UI — the bug is in the assertion, not the component.

Related: [[feedback_playwright_assertions]] for the retrying-assertion rule.
