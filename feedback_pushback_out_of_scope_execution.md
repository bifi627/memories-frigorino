---
name: feedback_pushback_out_of_scope_execution
description: During plan execution, actively push back on out-of-scope requests — flag, defer to IDEAS.md, continue the plan — don't silently absorb the tangent
metadata:
  node_type: memory
  type: feedback
  originSessionId: e9ca11eb-a583-4223-ac97-e6b4d9167701
---

During plan execution, when a user request would expand scope beyond the committed plan (e.g. a shared-component redesign while mid page-recompose), flag it as out-of-scope in one line, defer it to `IDEAS.md`, and continue the plan. Don't start the tangent even if it seems small.

**Why:** the user explicitly asked to be held to scope — *"ok wait with the #3, we defer it for later as planned you are right, push me back when i go out of scope"* — after raising a compact-row-density tweak mid-Phase-3.

**How to apply:** treat the approved plan as the scope boundary; new asks during execution get acknowledged, parked in `IDEAS.md`, and revisited after the plan ships. This is active scope-defense during execution — distinct from [[feedback_exploratory_pacing]] (pacing while the user is still exploring options). Related: [[feedback_remove_tracking_items_when_done]].
