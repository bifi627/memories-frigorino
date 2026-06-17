---
name: feedback_api_regen_workflow
description: After backend API changes, regenerate the frontend TS client with a single `npm run api` from `ClientApp/`. No backend boot, no DB, no manual copy — the build-time MSBuild target emits the spec.
type: feedback
originSessionId: d41c0617-780f-4096-a621-41d98da661fd
---
After any backend change that affects the API surface (controllers or `Frigorino.Features` slices), regenerate the TypeScript client with one command:

```bash
cd "C:/Repositories/frigorino/Application/Frigorino.Web/ClientApp" && npm run api
```

This runs `api:fetch` (`dotnet build ..` — emits `src/lib/openapi.json` via the `Microsoft.Extensions.ApiDescription.Server` MSBuild target) followed by `api:gen` (runs `openapi-typescript-codegen` against that spec to regenerate `src/lib/api/`).

After regen, update any frontend callsites whose generated method names changed (typically in `src/hooks/use*Queries.ts`), then `npm run build` to verify TypeScript still compiles.

**Why this works without a running backend:** OpenAPI generation now happens at build time via `Microsoft.Extensions.ApiDescription.Server`. The build runs the app's entry point with a mock server. Code that requires real config (Firebase auth, EF migrations) is gated in `Program.cs` behind `Assembly.GetEntryAssembly()?.GetName().Name == "GetDocument.Insider"`. So no Postgres connection, no user-secrets, no Firebase service account JSON is needed.

**Two-step iteration:** if you only want to re-run codegen against the existing `openapi.json` (e.g. to tweak codegen flags), use `npm run api:gen` directly — skips the dotnet build.

**How to apply:** Use this whenever the wire format changes — new endpoint, new DTO field, renamed tag, etc. The user does NOT need to start the backend manually; do the whole flow yourself with the single command. (The old multi-step recipe — start backend with `--launch-profile Backend`, curl `/swagger/v1/swagger.json`, copy file, then `npm run api` — was retired in May 2026 when the project migrated from Swashbuckle to `Microsoft.AspNetCore.OpenApi` + Scalar.)
