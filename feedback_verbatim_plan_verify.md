---
name: feedback_verbatim_plan_verify
description: Verbatim-from-plan code passing tsc+lint+verbatim-match does NOT prove correctness — plan-baked semantic/runtime bugs survive static checks; per-task code-quality pass on UI/logic files + manual browser verification is the real net
metadata:
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

When executing a plan that contains full code (subagent-driven-development with copy-this-exactly tasks), a bug baked into the plan is reproduced faithfully and passes type-check, lint, the implementer's self-review, AND a "matches the plan verbatim" file read. Static checks and verbatim-match are necessary but NOT sufficient.

**Why:** In the composer-input-redesign session the `<div>`-in-`<p>` hydration bug (a MUI `<Chip>` rendered inside `<Typography variant="body2">`) was copied verbatim from the approved plan. It cleared tsc, lint, the implementer self-review, and my own verbatim-match check. It was only caught by manual browser verification (console hydration error at the autocomplete dropdown), then fixed. I had also skipped subagent-driven-development's mandated per-task code-quality review on the mechanical tasks, so nothing inspected the JSX for DOM-nesting correctness until the end.

**How to apply:** For verbatim mechanical tasks it's fine to skip the spec reviewer and self-verify pure type/config files by reading them. But (1) run at least a lightweight code-quality pass on non-trivial UI/logic files per task (catches DOM-nesting, effect-deps, etc. that verbatim-match can't), and (2) always do the manual browser verification for UI changes — it's the only thing that catches plan-baked DOM/runtime bugs. Treat "matches the plan exactly" as "faithfully reproduced", not "correct". See [[feedback_scope_verification_by_confidence]] and [[feedback_verify_with_full_tests_and_docker]].

**Plan-authoring corollary (recipe copy-to-list session, 2026-06-16):** the bugs can be in the plan's *test* code too, and they're subtler than DOM nesting. That plan shipped an internally self-contradictory IT step — the arrange sent `quantity: null` while the assert checked `quantity 250 unit 0`, so the test would have failed against itself — plus IT helpers missing the `AuthHeaders` every sibling helper sets (silent 401), plus two generated/framework symbol names that didn't exist (`getListItemsQueryKey` → real `getItemsQueryKey`; `ScenarioContextHolder.BaseUrl` → absent). Caught only by reading the real files before writing + running each TDD step. So when writing plans with verbatim code: (a) self-review tests for **arrange⇄assert consistency** (does the setup actually produce what the assertion checks?), not just type consistency; (b) treat every referenced generated/codegen/framework symbol as **VERIFY-at-plan-time** (grep the api-gen + infra), not "confirm later" — a hedged "open confirmation" in the plan still ships wrong concrete code.
