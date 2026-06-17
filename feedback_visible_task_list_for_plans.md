---
name: feedback_visible_task_list_for_plans
description: "For multi-task plan execution (subagent-driven/executing-plans), always maintain a visible TaskCreate/TaskUpdate list in the chat"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 15f657e0-9160-445f-92a9-a0e7c5c5e418
---

When executing a multi-task implementation plan (e.g. superpowers subagent-driven-development or executing-plans, or any work spanning ≥3 tracked tasks), always create a visible task list in the chat via TaskCreate up front and keep it current with TaskUpdate (in_progress when starting, completed when done) as each task lands.

**Why:** The user wants to watch progress on long plan executions; prose-only status isn't enough — they asked for a proper visible task list for this type of work.

**How to apply:** At the start of plan execution, mirror each plan task as one TaskCreate item (T1..Tn). Flip to in_progress before dispatching the implementer subagent and to completed after review passes. Relates to [[feedback_scope_verification_by_confidence]].
