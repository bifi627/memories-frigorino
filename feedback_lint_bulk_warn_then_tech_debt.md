---
name: feedback_lint_bulk_warn_then_tech_debt
description: "When a newly-enabled lint rule set fires broadly across intentional-but-advisory patterns, downgrade ONLY the specific failing rules to warn + log sites in TECH_DEBT.md — don't add per-site eslint-disable for bulk findings."
metadata:
  node_type: memory
  type: feedback
  originSessionId: eedddc4f-9c4a-4ffb-9300-7a22a0436eab
---

When enabling a new lint rule set (e.g. React Compiler rules) produces many advisory warnings across intentional, correct patterns, downgrade **only the specific rules currently failing** to `warn` (not `error`) so CI stays green, keep correctness-critical rules at `error`, and log the sites in TECH_DEBT.md with a "flip back to error once cleared" note.

**Why:** lets the codebase be cleaned up later without blocking the current change. Per-site `eslint-disable` is for *individual* targeted violations; a blanket of advisory findings shouldn't be papered over with disables.

**How to apply:** identify the exact rules failing, set just those to `warn`, leave the rest at `error`, add a TECH_DEBT.md entry. Quote: *"go with the warn path, make sure we only warn for these specific rules we currently fail, add to tech debt"* (preceded by *"I want to have a clean codebase so I lean towards fixing all issues, but I don't have time to do this right now"*). Contrast [[feedback_eslint_disable_reset_on_open]] (targeted inline disable for one legitimate violation). Related: [[feedback_npm_scripts]], [[feedback_prettier_in_verification]].
