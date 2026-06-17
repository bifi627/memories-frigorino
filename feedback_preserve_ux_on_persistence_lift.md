---
name: feedback_preserve_ux_on_persistence_lift
description: "When a task is \"lift state to DB / persist X\", preserve the existing UX exactly — don't redesign affordances"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: efed9f3d-ec80-4b30-a6dd-cc92366cffe5
---

When the task is to **move/persist existing state** (e.g. localStorage → DB so it can be shared across users), the scope is the storage layer and sharing — **not** the UX. Preserve every existing affordance exactly unless the user explicitly asks to change it.

**Why:** During the promote-batch persistence brainstorm I twice drifted into redesigning the promote sheet (proposed dropping the per-item "X" button, making skip "apply on Confirm" instead of immediate). The user pushed back: "revisit the implementation," then listed the 7 existing behaviors to preserve, and stated the goal plainly — "main goal is to lift this state into database so multiple users can share the promotion state."

**How to apply:** Read the *actual* current implementation (not a summary) before proposing changes to a feature that already exists. Frame the design as "same behavior, different storage." If a UX change seems beneficial, surface it as an explicit optional question, don't bake it into the design.
