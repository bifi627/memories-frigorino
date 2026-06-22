---
name: feedback_flag_spec_contradictions_before_complying
description: When a user instruction contradicts a just-written spec/plan or a known convention, flag it in one line before acting — don't silently comply
metadata:
  node_type: memory
  type: feedback
  originSessionId: 96057bd1-e155-4863-a199-cfbc023067a7
---

When an instruction contradicts a just-written spec/plan or a known project convention, surface the conflict in one line **before** acting — e.g. *"heads-up, the spec defers this to ship-time — still remove it now?"* — then do what the answer says. Silent compliance triggers an edit → reject → restate loop that wastes a turn and trains mixed signals.

**Why:** the user said *"also remove the entry from ideas"*; the agent silently complied, the edit was rejected, and the user restated *"leaving it in place… we'll remove when it ships"* — exactly the deferral the plan under review already specified ([[feedback_remove_tracking_items_when_done]] says delete a tracking entry only once its work ships). A one-line flag would have avoided the whole loop.

**How to apply:** before complying with an instruction that conflicts with the active spec/plan or a saved convention, name the conflict and ask. Related: [[feedback_remove_tracking_items_when_done]], [[feedback_preserve_ux_on_persistence_lift]].
