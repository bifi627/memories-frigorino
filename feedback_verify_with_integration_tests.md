---
name: feedback_verify_with_integration_tests
description: "Run `dotnet test Application/Frigorino.sln` for full backend coverage. Do NOT parallelize verify steps that share state — neither a second `dotnet test` against IT (Testcontainers port), nor `npm run build` alongside the sln (clobbers ClientApp/build/)."
metadata:
  node_type: memory
  type: feedback
  originSessionId: bc518481-b2a6-44a2-8b7d-afe8703383d0
---

For full backend verification, run **`dotnet test Application/Frigorino.sln`** — one command, one process, covers both `Frigorino.Test` (unit + ArchUnitNET) and `Frigorino.IntegrationTests` (Reqnroll + Playwright + Postgres Testcontainers).

**Why:** Integration tests (~2 min) are part of the default verification scope — they catch SPA + backend regressions unit tests miss. The sln runs them in the same process, sequenced, so there's exactly one Testcontainers postgres and one SPA build.

**Two parallel-verify pitfalls — both look like flaky IT but are deterministic races:**

1. **Two `dotnet test` invocations against IT in parallel** → both spin up Testcontainers postgres on the same port → Reqnroll `OnTestRunStartAsync` explodes → all 58 tests fail in <100ms with a stack trace that hides the port collision.
2. **`npm run build` parallel with `dotnet test Application/Frigorino.sln`** → both write to `Application/Frigorino.Web/ClientApp/build/` (the sln test indirectly triggers `npm run build` via the Web project's `SpaPublishCommands` MSBuild target). The race produces a half-written `build/` that Playwright loads → 30s navigation timeouts on all IT tests. `npm run build` is also REDUNDANT when running the sln — MSBuild does it anyway.

**How to apply:** When the user asks for "verify" / "run tests" / "make sure it works", or when I'm about to declare a non-trivial change done: run the sln. Sequence frontend checks (tsc/lint) BEFORE the sln test — don't background them in parallel. Skip a standalone `npm run build` if the sln test is also queued. Only target a specific project (`dotnet test Application/Frigorino.Test` or `...IntegrationTests`) when intentionally narrowing scope — and never run two `dotnet test` commands against the same IT project concurrently. Docker daemon must be up for Testcontainers (see [[feedback_docker_daemon_check]]); if it fails for that reason, prompt the user, don't skip.

Related: [[feedback_verify_with_full_tests_and_docker]] established `dotnet test` + `docker build` as the verification bar; `deps-bump` skill's per-wave verify section relies on this sequencing.
