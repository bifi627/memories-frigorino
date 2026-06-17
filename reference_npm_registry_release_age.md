---
name: reference_npm_registry_release_age
description: "This env's npm install blocks too-recently-published versions (min-release-age)"
metadata: 
  node_type: memory
  type: reference
  originSessionId: ca2b782a-621a-44a3-973b-f5d10b41db8a
---

In this environment `npm install` enforces a **`--min-release-age`** constraint: versions published within ~the last few days are rejected (`ETARGET ... No matching version found ... with a date before <cutoff>`). `npm view <pkg> version` reports the true latest, but installing `@latest`/a too-new exact pin fails.

Workaround: install with a **broad caret** (`pkg@^9.0.0`) so npm resolves the newest *allowed* version, then read the resolved version from `package-lock.json` and pin `package.json` to caret-minor of THAT (per [[feedback_dependency_pinning]]). Also incompatible: `--min-release-age` cannot be combined with `npm view --before` (errors out).

Example: `@mui/x-date-pickers@^9.4.0` failed (9.4.0 too new); `^9.0.0` resolved to 9.3.0.
