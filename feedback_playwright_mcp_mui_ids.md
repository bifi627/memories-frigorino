---
name: feedback_playwright_mcp_mui_ids
description: When driving the SPA via Playwright MCP, re-query MUI inputs by placeholder/role right before typing — don't reuse a cached :r: id; MUI regenerates them when panels mount/unmount
metadata:
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

When driving the Frigorino SPA via Playwright MCP (after `dev-up`), do not reuse a MUI-generated element id (the `_r_x_` / `:r:` ids) captured from an earlier snapshot. MUI regenerates these ids whenever components mount/unmount (e.g. opening/closing the composer's quantity or expiry panel), so a cached id can now point at a different input.

**Why:** Verifying the inventory composer, I reused `#_r_e_` and typed "Yogurt" into the quantity ("Menge") field instead of the autocomplete — because opening the expiry/quantity panels had shifted the ids. Cost a few wasted tool calls to detect (`qtyVal: "Yogurt"`) and fix.

**How to apply:** Target inputs by stable attributes — `placeholder`, `role="combobox"`, or a `data-testid` (the composer keeps `autocomplete-input-textfield` / `autocomplete-input-submit-button`) — and re-query immediately before typing, especially after any action that mounts/unmounts UI. If you must use a raw id, re-read it from a fresh snapshot first. Related: [[feedback_playwright_assertions]] (that one is about C# Reqnroll test-step assertions; this is about MCP browser-driving).
