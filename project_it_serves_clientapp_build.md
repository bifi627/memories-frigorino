---
name: project_it_serves_clientapp_build
description: "Frigorino integration tests serve the SPA from ClientApp/build — rebuild after editing React source or new testids/changes won't appear"
metadata: 
  node_type: memory
  type: project
  originSessionId: 15f657e0-9160-445f-92a9-a0e7c5c5e418
---

The Reqnroll+Playwright integration harness (`Frigorino.IntegrationTests`) serves the SPA from `Application/Frigorino.Web/ClientApp/build`, not from live source. `TestWebApplicationFactory` does `UseWebRoot(.../ClientApp/build)`, and `SpaBuildHelper.EnsureSpaIsBuilt()` only rebuilds when `src` is newer than `build/index.html`.

**Why it matters:** if you edit React source for a UI test (e.g. add `data-testid`s), the browser keeps seeing the OLD assets until you run `npm run build` from `ClientApp/`. Elements simply won't be found and the failure looks like a bad selector, not a stale build. `ClientApp/build` is gitignored — don't commit it; commit only the source + `.feature` + step files.

**How to apply:** after any React source change that a UI integration test depends on, run `npm run build` before `dotnet test ...IntegrationTests`. Relates to [[feedback_it_mui_select_playwright]] and the per-feature `*.Api.feature` + UI `*.feature` convention.
