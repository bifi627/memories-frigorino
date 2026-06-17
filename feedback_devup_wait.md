---
name: feedback_devup_wait
description: "How to wait for the dev stack to come up — one mechanism, no ScheduleWakeup misuse"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 541cc763-142b-4ee5-87a8-2789ba4fcce7
---

When bringing up the local dev stack (`scripts/dev-up.ps1`, the `dev-up` skill), use ONE wait mechanism, not three.

- **Check if it's already up BEFORE running the script** (avoid a *redundant* bring-up — the user has flagged this: "check the status, i think the env is up"). First read `.dev/stack.json` for the recorded `backendPort`/`vitePort`, then probe both (`curl -ks -o /dev/null -w "%{http_code}" https://localhost:<backendPort>/healthz` and the vite root). If both return 200, reuse it. This also avoids an unnecessary PowerShell invocation (see [[feedback_prefer_bash]]). Ports are per-worktree, so don't assume the canonical 5001/44375 — read stack.json. NOTE: you OWN the stack lifecycle (bring up / tear down on demand) — see [[feedback_dev_env_lifecycle]]; this supersedes any "the user keeps the stack alive across sessions / tear-down is theirs" reading of this note.
- `dev-up.ps1` already polls `/healthz` + the Vite root until both return 200, then exits. So when the script call completes successfully, the stack IS up — no extra watcher needed.
- **Do NOT passively wait for a background completion notification, and do NOT rely on the background-task OUTPUT FILE.** A long `dev-up.ps1` invocation gets auto-backgrounded by the Bash tool (or self-detaches), and in practice the wrapper does NOT reliably emit a completion signal — waiting on it stalls indefinitely (confirmed 2026-05-26 AND again 2026-06-03: both times I said "I'll wait for the notification" and hung until the user nudged me). The Bash background-task `.output` file is ALSO useless here — it stays empty / 1 line and does NOT stream the script's progress, so reading it tells you nothing. Instead, **probe directly**: the script writes `.dev/stack.json` with the per-worktree `backendPort`/`vitePort` early, and the endpoints become reachable once ready. Poll them yourself: `cat .dev/stack.json`, then `curl -ks -o /dev/null -w "%{http_code}" https://localhost:<backendPort>/healthz` and the vite root, and read `.dev/backend.out.log` / `.dev/vite.out.log` for state. Two 200s = up, proceed. This probe is the PRIMARY readiness mechanism, not a fallback — go straight to it after kicking off the script; never sit idle waiting for a signal that won't come.
- Do NOT stack a background log-grep watcher on top of the script AND a wakeup — that's what went wrong in the undo-delete session (the script's log was buffer-empty so the grep was fragile; the curl probe is what actually worked).
- Do NOT use `ScheduleWakeup` to wait for background work outside a `/loop` context — it's the wrong tool and it litters `.claude/scheduled_tasks.lock`. Background tasks notify on completion on their own.

**Why:** In the undo-delete run I over-engineered the dev-up wait (background script + background grep-watcher + ScheduleWakeup) for a simple "is it up yet" check. User flagged it in retro.

**How to apply:** Bring stack up → poll `.dev/stack.json` + curl `/healthz` and the vite root yourself until both 200 → proceed. Do not block on a background completion notification; it may never arrive.
