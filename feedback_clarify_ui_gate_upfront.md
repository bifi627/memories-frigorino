---
name: feedback_clarify_ui_gate_upfront
description: Clarify the UI-verification gate BEFORE implementing a UI change — the user often prefers to quick-test the UI themselves rather than have the agent spin up the dev stack; agent-driven verify is for background/unattended runs
metadata:
  node_type: memory
  type: feedback
  originSessionId: e9ca11eb-a583-4223-ac97-e6b4d9167701
---

Before implementing a UI change, clarify how it will be verified — don't reflexively run `dev-up` / drive Playwright. Over recent weeks the user has found it more efficient to do a quick manual test themselves than to have the agent bring up the dev stack. So default to handing off: name the route/page and exactly what to check, then stop. Reserve agent-driven dev-stack verification for **background/unattended** runs, where it's easier to let the agent drive.

**Why:** user — *"over the last weeks i realized its more efficient to do a quick test myself before letting the agent run the dev stack, the agent should clarify before the implementation how the ui gate should be, sometimes its better to do the ui test myself, sometimes when running in the background its easier to let the agent do the dev stack."* Held across 4 checkpoints in one session (*"i will test it myself"*).

**How to apply:** at the start of UI work, ask which gate applies (user-verifies vs agent-drives). Interactive session → default user-verifies, hand off the route + check-list. Background/unattended → agent drives the stack. Related: [[feedback_verbatim_plan_verify]] (manual browser verify is still the net — but the verifier is usually the user), [[feedback_dev_env_lifecycle]] (you still own the stack lifecycle WHEN the agent is the one verifying), [[feedback_devup_wait]].
