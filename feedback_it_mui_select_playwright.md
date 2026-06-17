---
name: feedback_it_mui_select_playwright
description: Driving MUI inputs in Playwright IT — Select testid lands on FormControl not the combobox; wait out the data-load useEffect before filling
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 15f657e0-9160-445f-92a9-a0e7c5c5e418
---

Two recurring traps when Playwright-driving MUI components in the integration tests (and via Playwright MCP):

1. **MUI `TextField select`:** a `data-testid` on the `TextField` lands on the outer `FormControl` `<div>`, which does NOT open the dropdown. Click the inner `role="combobox"` instead: `GetByTestId("x").GetByRole(AriaRole.Combobox).ClickAsync()`, then click the option. To assert the selected value without reading translated text, put a testid on the hidden `<input>` via `slotProps={{ htmlInput: { 'data-testid': '...' } }}` and use `ToHaveValueAsync(code)`. For plain inputs/switches also prefer `slotProps.htmlInput` so the testid is on the real `<input>` (makes `FillAsync`/`ToHaveValueAsync`/`ToBeDisabledAsync` reliable).

2. **Render race vs. data fetch:** cards that seed an input from a GET via `useEffect` will clobber a value typed before the GET resolves. In the step, wait for the field to hold a non-empty/loaded value before `FillAsync`.

**Why:** these cost ~20 min of churn writing the settings UI tests; both look like bad selectors but are timing/structure issues.

**How to apply:** when writing UI `*.feature` steps, target MUI selects by combobox role, expose values via `slotProps.htmlInput` testids, and gate fills on the load. Relates to [[feedback_playwright_mcp_mui_ids]], [[feedback_playwright_assertions]], [[feedback_test_assertions_no_translated_text]], [[project_it_serves_clientapp_build]].
