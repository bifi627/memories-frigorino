---
name: project_branch_workflow
description: "Frigorino uses stage/main two-branch Railway model; stage is a real client UAT environment, not a CI sandbox"
metadata: 
  node_type: memory
  type: project
  originSessionId: 324808ad-fad5-41b8-8270-6f88e69a3c34
---

Frigorino has two Railway-connected branches: `stage` (UAT at stage-frigorino.up.railway.app, used by a real client) and `main` (prod). Workflow is lightweight Gitflow: feature branches → PR → `stage` (squash merge), client validates on stage URL, then `stage → main` is promoted via fast-forward (PR or `git push origin stage:main`). `main` must stay a strict ancestor of `stage` to avoid permanent divergence.

**Why:** Client uses the stage URL for UAT, so `stage` stability is load-bearing — not a place to probe CI or land throwaway commits. Promotion cadence is client-sign-off driven, not CI-green driven.

**How to apply:**
- Never suggest pushing test/probe commits to `stage` (e.g. the `814d16d "test change"` whitespace tweak was a mistake) — use a throwaway `ci/*` branch + `workflow_dispatch` for CI experiments.
- Never suggest direct commits or non-FF merges to `main` — would break the ancestor invariant and pollute future stage→main PRs.
- Hotfix flow: branch off `main` → PR → `main`, then immediately `git checkout stage && git merge --ff-only main && git push` so stage stays caught up.
- When opening stage→main PRs, default to "Rebase and merge" or "Merge" only if FF is possible; "Create a merge commit" breaks linear history.
- Branch protection: both branches should require linear history to mechanically enforce this.
