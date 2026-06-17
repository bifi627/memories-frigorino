---
name: reference_reqnroll_keyword_sensitive
description: "This repo's Reqnroll matches step bindings keyword-sensitively (Given≠When≠Then), unlike SpecFlow default"
metadata: 
  node_type: memory
  type: reference
  originSessionId: bd8d3493-1d42-494c-923e-30c3419fa134
---

In Frigorino.IntegrationTests, Reqnroll matches steps to bindings by **keyword type**, not just text — a `[When("...")]` binding will NOT match a step that resolves to a `Given` (e.g. an `And` following a `Given`), and Reqnroll reports it as "No matching step definition found" (a silent-looking failure suggesting a new binding). This differs from SpecFlow's keyword-agnostic default.

**How to apply:** A setup/action step reused under different keywords must be **double-decorated**, e.g.
`[Given("...")]` + `[When("...")]` on the same method. Precedent: `Slices/Inventories/ExpiryCalendarApiSteps.cs`. Bit the recipe-sections POST-section step (used as `When` in one scenario, `And`-under-`Given` in others). Note `And` inherits the *preceding* keyword, so the same step text can resolve to When in one scenario and Given in another. Related: [[reference_reqnroll_filter_silent_skip]].
