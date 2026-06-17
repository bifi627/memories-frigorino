---
name: feedback_eslint_disable_reset_on_open
description: "Reset-on-open effects that trip the react-hooks/set-state-in-effect ESLint rule use a targeted inline eslint-disable/enable block, not a file- or hook-level disable."
metadata:
  node_type: memory
  type: feedback
  originSessionId: a415f606-6127-443b-9933-b0bfb68f5b03
---

When a component legitimately re-seeds local state in a `useEffect` keyed on an `open`/visibility prop (a sheet/dialog resetting its form when reopened), the `react-hooks/set-state-in-effect` lint rule fires. The established codebase pattern is a **targeted inline** `/* eslint-disable react-hooks/set-state-in-effect */ … /* eslint-enable react-hooks/set-state-in-effect */` block wrapping just that effect — not a file-level disable and not a blanket hook-level one.

**Why:** keeps the escape hatch scoped to the one legitimately-correct reset-on-open effect, so the rule still guards the rest of the file. Precedent: `MediaPreviewSheet.tsx`.

**How to apply:** match the existing sheets/dialogs when adding a reset-on-open effect — wrap the single effect in the targeted disable/enable comment pair. Related: [[feedback_object_url_strictmode]] (another StrictMode/effect-discipline rule), [[feedback_npm_scripts]].
