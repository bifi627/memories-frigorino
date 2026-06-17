---
name: feedback_dev_env_lifecycle
description: "Agent owns the local dev-stack lifecycle — bring it up with /dev-up only when UI verification needs it, and tear it down with /dev-down yourself when done or when it conflicts with tests (a running backend locks bin/Debug DLLs and blocks `dotnet test`). Prefer ephemeral on-demand up/down over long-running."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

Manage the local dev stack's lifecycle yourself. Don't ask the user to decide dev-up/dev-down for your own verification needs — just do it.

**Why:** A long-running dev-stack backend holds `Application/Frigorino.Web/bin/Debug/**/*.dll` locked, so `dotnet test` fails to rebuild Frigorino.Web (`MSB3021`/`MSB3027` "being used by another process") and the IntegrationTests never run. When this happened the user said: handle the dev env yourself; if it's up and conflicts with the integration tests you can always tear it down again with /dev-down; prefer bringing it up and down on demand instead of letting it run for a long time.

**How to apply:**
- Bring the stack up (/dev-up) only when you actually need the running UI; tear it down (/dev-down) when that verification is done.
- **Before invoking `dev-up.ps1`, check if the stack is already up:** the user frequently keeps it running. Read `.dev/stack.json` for the recorded `vitePort`/`backendPort` and drive Playwright MCP against `https://localhost:<vitePort>/` directly. In one session the user rejected my `dev-up.ps1` run and said "continue with dev environment" — i.e. reuse the running stack, don't re-launch the script when it's already up.
- If `dotnet test` fails to build because a running `Frigorino.Web` process locks the bin/Debug DLLs, run /dev-down yourself, then re-run the tests — don't pause to ask.
- Don't leave a stack running long-term "just in case"; ephemeral up/down is preferred.
- This OVERRIDES the older "tear-down is the user's call / the user keeps the stack up across sessions" stance (the dev-up skill's "don't auto-invoke /dev-down" note and [[feedback_devup_wait]]). Still avoid *redundant* bring-ups — use [[feedback_devup_wait]]'s "is it already up?" check and single wait mechanism.

Related: [[project_devup_data_disposable]] (stack data is throwaway, so tearing down and rebuilding is cheap), [[feedback_verify_with_integration_tests]], [[feedback_verify_exit_code]].
