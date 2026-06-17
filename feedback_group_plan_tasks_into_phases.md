---
name: feedback_group_plan_tasks_into_phases
description: "For complex features with >9 plan tasks, group tasks into logical phases to implement iteratively"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: bd8d3493-1d42-494c-923e-30c3419fa134
---

When an implementation plan for a complex feature has more than ~9 tasks, group the tasks into a small set of logical phases (e.g. Backend / Frontend / Finals) and execute phase-by-phase in iteration, reviewing at each phase boundary.

**Why:** Long flat task lists are hard to track and review; phase boundaries give natural, independently-reviewable checkpoints and keep iteration focused.

**How to apply:** Add an "Execution phases" overview near the top of the plan + a phase header (`#`) with a one-line intro before each phase's first task. Keep task numbers stable (tasks cross-reference each other by number) — phases group tasks, they don't renumber them. Pick seams where the work is independently reviewable (e.g. the TS-client regen sits at the backend/frontend seam). Pairs with subagent-driven execution: fresh subagent per task, review between tasks, opus for the end-of-phase holistic review. See [[feedback_visible_task_list_for_plans]], [[feedback_opus_for_final_holistic_review]].
