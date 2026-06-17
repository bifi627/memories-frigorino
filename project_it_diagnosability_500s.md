---
name: project_it_diagnosability_500s
description: "Diagnosability affordances for 500s/IT failures — server log dump, ProblemDetails, /api 404 fallback"
metadata: 
  node_type: memory
  type: project
  originSessionId: 4e97af29-79d8-407e-a89e-255f65c8bbaa
---

Three diagnosability fixes shipped (TECH_DEBT item "Unhandled 500s near-undiagnosable"):

1. **IT failures now dump server logs.** On a failing Reqnroll scenario, the host's buffered `Warning`+ logs (incl. the framework's `Error`-level log of any unhandled exception) print to the `dotnet test` output between `===== Server logs (Warning+) for failed scenario =====` markers. Wired via `InMemoryLogSink`/`InMemoryLoggerProvider` (registered in `TestWebApplicationFactory.ConfigureLogging`) + `ServerLogDumpHook` (`[AfterScenario(Order=1)]`, gated on `ScenarioContext.TestError != null`). So: when an IT goes red with an opaque 500, **read the dump first** — the real exception is right there now.
2. **Unhandled exceptions → ProblemDetails.** `app.UseExceptionHandler()` is active in **non-Development** envs (prod + IntegrationTest); Development keeps the dev exception page. 500s carry `application/problem+json` with `type`/`status`/`traceId` (no stack trace leaked). Grep logs by `traceId`.
3. **Unmatched `/api/*` → 404, not SPA shell.** `app.MapFallback("/api/{**rest}", () => Results.NotFound())` sits before `MapFallbackToFile("index.html")` so a route-constraint miss (e.g. out-of-int-range `{id}`) reads as 404, not a 500 from `SpaDefaultPageMiddleware`. This was the original [[project_optimistic_tempid_reconcile]] failure mode.

Verify pattern used: temp `IsEnvironment("IntegrationTest")`-gated throwing endpoint + temp scenario, run, observe, revert.
