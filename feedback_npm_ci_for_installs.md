---
name: feedback_npm_ci_for_installs
description: Use npm ci to install ClientApp deps (fresh clone/worktree); npm install only when adding or updating dependencies
metadata: 
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

For the frontend (`Application/Frigorino.Web/ClientApp`), install dependencies with `npm ci`, not `npm install`.

**Why:** `npm ci` installs exactly from `package-lock.json` (reproducible) and never mutates the lockfile. `npm install` can silently change the lockfile, which undermines the lockfile-as-reproducibility-guarantee (see [[feedback_dependency_pinning]]).

**How to apply:** Use `npm ci` for clean installs — fresh clone, new worktree, CI. Only use `npm install`/`npm i <pkg>` when the intent is to actually add a new dependency or update an existing one. Still go through package.json scripts for tooling (see [[feedback_npm_scripts]]).
