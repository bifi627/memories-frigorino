---
name: feedback_dockerfile_csproj_sync
description: After adding/removing/renaming .NET projects, update Application/Dockerfile and always run a local docker build at the end of any bigger feature
type: feedback
originSessionId: 9d607f26-7895-4265-bdd5-280b84aa5fa2
---
After major structural changes to the .NET solution (adding, removing, or renaming projects), update `Application/Dockerfile` to match. The restore layer copies csproj files **explicitly per project**, not via `COPY . .`, so new projects in the sln won't be present when `dotnet restore` runs in the container. At the end of any bigger feature, **always run a local `docker build` to verify** before declaring the feature done — don't wait for Railway to discover the breakage.

**Why:** The Railway build failed on 2026-05-06 because `Frigorino.Features` (added in commit `2078b1f`) and `Frigorino.IntegrationTests` (added in commit `fdc7a9e`) were added to `Frigorino.sln` but never added to the Dockerfile. `dotnet restore` failed with `MSB3202: project file ... was not found`. The per-csproj COPY pattern is intentional for cache granularity, but it makes the Dockerfile silently out-of-date when projects are added — and `dotnet build`/`dotnet test` on the host will pass even when the container build is broken, so only an actual docker build catches the drift.

**How to apply:**
1. Any change that touches `Application/Frigorino.sln` (project added/removed/renamed) or creates a new `Application/<NewProject>/` directory must also update the COPY list in `Application/Dockerfile` (currently lines ~12–16).
2. At the tail end of any bigger feature (new project, new build step, new asset pipeline, new env var the Dockerfile cares about, etc.), run `docker build -f Application/Dockerfile -t frigorino .` from the repo root and confirm it succeeds. Treat this as part of "done", same as tests passing. Railway uses the same Dockerfile and will fail the same way otherwise.
