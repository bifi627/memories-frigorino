---
name: feedback_nuget_lockfile_force_evaluate
description: "Refreshing packages.lock.json after adding/removing a NuGet package needs `dotnet restore --force-evaluate`, not plain/--force restore"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a570e39f-45dd-4d89-9691-c47573b92aef
---

In Frigorino (NuGet `RestorePackagesWithLockFile`, Docker restores with `--locked-mode`), after **removing** (or adding) a `PackageReference`, plain `dotnet restore` reports "all projects up to date" and `--force` only re-downloads — neither recomputes the lock-file dependency graph, so a removed package lingers in dependent projects' `packages.lock.json` (transitive-graph nodes go stale). Use `dotnet restore Application/Frigorino.sln --force-evaluate` to actually regenerate the locks, then `grep` the lock files to confirm the package is gone from ALL of them (it propagates into Web/Test/IntegrationTests/Features locks, not just the project that referenced it).

**Why:** stale locks + `--locked-mode` Docker restore = a build that passes locally but the committed locks misrepresent the graph.
**How to apply:** any add/remove of a NuGet dep → `--force-evaluate` restore → grep-verify across all `Application/*/packages.lock.json` → commit the lock changes. Relates to [[feedback_dependency_pinning]] and the `deps-bump` workflow.
