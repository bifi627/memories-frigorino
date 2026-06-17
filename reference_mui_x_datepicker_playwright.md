---
name: reference_mui_x_datepicker_playwright
description: Driving a MUI X DatePicker field in Playwright IT — testid placement + deterministic locale
metadata: 
  node_type: memory
  type: reference
  originSessionId: ca2b782a-621a-44a3-973b-f5d10b41db8a
---

MUI X `DatePicker` (`@mui/x-date-pickers`, date-fns adapter via `AdapterDateFns` for date-fns v4 — NOT `AdapterDateFnsV3`, which isn't exported) renders an accessible **sections** field: editing happens in a visible segmented container, while the actual value lives in a separate **hidden, `aria-hidden`, `tabindex=-1` `<input>`**. Consequences for Playwright integration tests:

- A `data-testid` on `slotProps.textField.slotProps.htmlInput` lands on the **hidden** input → not clickable/fillable → 30s timeout. Put the testid on a **wrapping element** (e.g. a `<Box data-testid>` around the DatePicker) instead. `FillAsync` does NOT work on the masked field at all.
- To set a date: `field.ClickAsync()` (focuses first section) then `Page.Keyboard.TypeAsync(digits)` in the **locale's section order** (en-US `MM/dd/yyyy` → `MMddyyyy`). To clear: click, `Keyboard.PressAsync("ControlOrMeta+a")`, `"Delete"`.
- The mask/format is locale-driven, so the IT browser locale MUST be deterministic. `ScenarioHooks.cs` `NewContextAsync` now sets `Locale = "en-US"` (was implicitly the host locale — German on this dev box → dd.MM.yyyy → broke digit order). i18n detection is `localStorage → navigator → htmlTag`; fresh contexts have empty localStorage so navigator wins.
- Guard silent digit-drop: after typing, assert the hidden input value: `Expect(field.Locator("input")).ToHaveValueAsync("MM/dd/yyyy")`.

Shipped in `feat/keyboard-expiry-dates` (shared `ExpiryDatePicker` wrapper). See [[feedback_test_assertions_no_translated_text]], [[feedback_it_mui_select_playwright]].
