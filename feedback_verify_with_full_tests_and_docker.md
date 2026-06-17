---
name: feedback_verify_with_full_tests_and_docker
description: "After a major change (feature, refactor touching multiple layers, infra change) run the FULL test suite (`dotnet test Application/Frigorino.sln` — includes IntegrationTests) AND a `docker build -f Application/Dockerfile -t frigorino:rc .` before declaring done. Unit tests alone are not sufficient verification."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: d02f623c-70b3-497a-a6ca-a904a7a9bf33
---

After any major change (feature spanning multiple layers, refactor of shared infra, middleware/pipeline changes, Dockerfile/csproj touch, dependency bumps), run BOTH:

1. The full test suite — `dotnet test Application/Frigorino.sln` (covers `Frigorino.Test` unit + ArchUnit AND `Frigorino.IntegrationTests` Reqnroll/Playwright/Testcontainers).
2. A Docker image build — `docker build -f Application/Dockerfile -t frigorino:rc .` (catches Dockerfile/csproj drift, RID-pinning issues, SPA build interaction, distroless runtime mismatches before Railway does).

**Why:** Unit tests alone do not catch:
- Pipeline-order bugs in `Program.cs` (middleware, options, fallback routing) — only the integration host exercises them.
- SPA build interactions with the backend (e.g. `.br`/`.gz` siblings present in wwwroot, `index.html` content-type via `MapFallbackToFile`).
- Dockerfile drift: missing csproj copies, RID/`PublishReadyToRun` interaction, distroless image lacking a shell when something assumes one.
- Concrete example: a static-files middleware change passed 158 / 158 unit + arch tests but silently introduced regressions in 10 Playwright UI scenarios — only caught when the full integration suite ran. Conversely the same change required a Docker build to validate that `--self-contained false -r linux-x64` actually produced a runnable image. See [[feedback_dockerfile_csproj_sync]] for the Dockerfile drift angle.

**How to apply:**
- Treat "all green" as `dotnet test` on the full SLN PLUS a successful `docker build`. Not one or the other.
- If the integration tests reveal pre-existing flakes unrelated to the change, say so explicitly — but still run them, don't skip.
- For trivial changes (typo fix, single-line tweak, docs) this rule does not apply; use judgment.
- If Docker daemon is unreachable, ask the user to start Docker Desktop rather than skipping the build — see [[feedback_docker_daemon_check]].
