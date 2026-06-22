---
name: feedback_writing_plans_for_code_not_docs
description: writing-plans / executing-plans superpowers skills are for code features only — for documentation-only work, treat the design spec as the plan and execute sequentially with phase-boundary reviews
metadata:
  node_type: memory
  type: feedback
  originSessionId: d0ac5e3d-4478-4aae-b786-df7f20c4488d
---

When a task is documentation-only (no code change), skip the `writing-plans` / `executing-plans` superpowers skills. They assume code: TDD task decomposition, red/green checkpoints, failing-test-first — none of which maps to prose. Instead treat the design/spec as the plan, execute it sequentially with a visible `TaskCreate`/`TaskUpdate` list, and review at each phase boundary. That gives the same checkpoint value without the code scaffolding.

**Why:** during a `knowledge/` restructuring session the user said *"i dont think writing-plan is the right direction since this is not a code feature but documentation feature, what do you recommend?"*

**How to apply:** docs/knowledge work → brainstorm → spec → sequential execution with a live task list + phase-boundary review; reach for `writing-plans`/`executing-plans` only when actual code changes are involved. Related: [[feedback_visible_task_list_for_plans]], [[feedback_group_plan_tasks_into_phases]], [[feedback_knowledge_docs_lookup_and_maintain]].
