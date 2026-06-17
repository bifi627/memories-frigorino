---
name: feedback_never_override_min_release_age
description: "Never use `npm install --min-release-age=0`; pick the latest version that clears the 7-day quarantine instead"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a94b496c-e3d4-4810-a387-5bdd3247e5bd
---

`npm install --min-release-age=0` is **NEVER allowed**, not even for local/throwaway investigation installs or testing "the latest" version. If a target version (e.g. `@tanstack/react-router@1.170.7`, published <7 days ago) is refused by the `.npmrc` `min-release-age=7` quarantine, find and install the **latest version that already clears the 7-day window** instead.

**Why:** The quarantine is a deliberate supply-chain defense (catches malicious releases before they're yanked). The user treats it as a hard guardrail with no exceptions. This is stricter than the `deps-bump` skill text, which said the override was "reserved for CVE patches" — the user has now ruled it out entirely. When in conflict, this rule wins.

**How to apply:** Before installing, if a version is fresh, check publish dates with `npm view <pkg> time --json` and choose the newest version dated ≥7 days before today. Never reach for `--min-release-age=0` (or `--force`/`--legacy-peer-deps` to paper over a quarantine-induced `@undefined` peer error). Related: [[feedback_dependency_pinning]].
