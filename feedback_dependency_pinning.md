---
name: feedback_dependency_pinning
description: NuGet `<PackageReference>` must be a full exact version (`10.0.7`). npm `dependencies`/`devDependencies` use caret-minor (`^1.2.3`); never `~`, `*`, or `x.*`. Lockfile is the reproducibility guarantee for npm.
type: feedback
originSessionId: d41c0617-780f-4096-a621-41d98da661fd
---
NuGet stays exact. npm allows `^` (caret-minor) — reverted from "exact-only" because exact pins make `npm audit fix` classify every advisory as breaking (no range overlap with patched-versions ⇒ requires `--force`), creating constant friction.

**Why:** Reproducibility comes from the lockfile, not from `package.json`. `package-lock.json` is committed and CI uses `npm ci`, so installs are deterministic regardless of declared ranges. The exact-pin rule was belt-on-suspenders that broke audit-fix without adding real reproducibility. NuGet is different: `packages.lock.json` + `--locked-mode` enforces lockfile fidelity, Dependabot opens one PR per package, and there's no `audit fix` equivalent to fight — so exact pins there cost nothing.

**How to apply:**
- New/edited `<PackageReference>` entries: always use `Version="x.y.z"` (complete, exact). Look up latest stable on nuget.org or via `dotnet package search <name>`. No `10.0.*`, no `2.*`.
- New/edited npm `dependencies`/`devDependencies`: use `^x.y.z` (caret-minor). Never `~` (patch-only is unnecessarily tight given the lockfile), never bare `*` or `x.*`.
- For npm bumps in `deps-bump` waves: edit `package.json` to the new `^x.y.z`, run `npm install` to refresh the lockfile, then verify. The lockfile diff is the source of truth for what actually moved.
- Docker base image pinning is a separate concern — confirm with the user before changing Dockerfile pins.
