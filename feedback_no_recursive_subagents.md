---
name: feedback_no_recursive_subagents
description: User dislikes recursive/nested subagents; only the main orchestrator may fan out work.
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 7acc5c1c-6790-4ab7-9f32-ac98b12735a2
---

User explicitly dislikes "recursive agents" — a spawned subagent spawning its own subagents. During subagent-driven plan execution, keep all fan-out (deciding next task, reviews, task tracking) in the MAIN session; spawned agents are sandboxed task-doers, not orchestrators.

**Why:** Recursive spawning is opaque (the user can't see who launched what), hard to attribute in the UI, and risks runaway cost/coordination. The user wants a flat one-level tree: orchestrator → workers.

**How to apply:**
- **Reviewer subagents:** dispatch with `subagent_type: "Explore"` — it structurally lacks the Agent tool (tools = all except Agent/Edit/Write/NotebookEdit/ExitPlanMode), so it can read code + run `dotnet test`/`git diff` but cannot spawn or edit. Ideal for read-only spec/quality reviews.
- **Implementer subagents:** they need Edit/Write so must be `general-purpose`/`claude` (no built-in tool-restricted *editing* agent exists). Add a firm clause to every implementer prompt: "Do NOT use the Agent/Task tools or spawn subagents — do all work yourself; if the task is too big, report BLOCKED instead of delegating."
- To continue a prior worker with its context, use SendMessage to its agentId (still one level — the orchestrator is driving). Relates to [[feedback_visible_task_list_for_plans]].
