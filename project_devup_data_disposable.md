---
name: project_devup_data_disposable
description: "Data in the dev-up (LocalDb bypass) environment is agent-seeded and disposable, not the user's real client data"
metadata: 
  node_type: memory
  type: project
  originSessionId: 541cc763-142b-4ee5-87a8-2789ba4fcce7
---

The lists/inventories/items visible in the local dev-up environment (LocalDb launch profile + `VITE_DEV_AUTH`, identity `dev@frigorino.local`, e.g. a "Wocheneinkauf" list) are seeded by me/agents in previous sessions — NOT the user's real client data.

**Why:** In the undo-delete retro I worried about having deleted/re-added items during manual UI verification. The user clarified it's the dev-up env, so that data is throwaway.

**How to apply:** Mutate dev-up data freely during UI verification (delete/restore/create) — no need to be precious about it or spin up a throwaway list first. Real client data lives on deployed `stage` (see [[project_branch_workflow]]), which I should never touch directly. Still leave the dev environment in a sane state when practical, but don't treat its data as precious.
