---
name: feedback_npm_scripts
description: Always invoke frontend tooling (lint, build, type-check, format, api regen) via the npm scripts defined in Application/Frigorino.Web/ClientApp/package.json — never bypass with raw npx eslint / tsc / prettier calls.
type: feedback
originSessionId: e95f7df5-e5df-45cd-b74c-f2146a93e340
---
For Frigorino's frontend (`Application/Frigorino.Web/ClientApp`), use the npm scripts defined in `package.json` (`npm run lint`, `npm run build`, `npm run fix`, `npm run api`, etc.) instead of invoking the underlying tools directly with `npx`.

**Why:** The scripts encapsulate the project's intended invocation (correct flags, file scopes, ordering — e.g. `build` runs `tsc -b && vite build`, `fix` runs `eslint . --fix && prettier --write .`). Calling `npx eslint <file>` or `npx tsc --noEmit` bypasses those guarantees, can produce different results from CI, and may miss configured project boundaries.

**How to apply:**
- Lint: `npm run lint` (not `npx eslint ...`).
- Type-check + build: `npm run build`. For type-check only, prefer extending the npm scripts if a dedicated one exists; if not, ask before introducing one.
- Auto-fix lint + format: `npm run fix`.
- Regenerate API client: `npm run api` (after backend produced fresh `swagger.json`).
- Run from the ClientApp directory: `cd "C:/Repositories/frigorino/Application/Frigorino.Web/ClientApp" && npm run <script>`.
- If you need a finer scope than what an npm script offers, ask the user whether to add a new script rather than reaching for `npx` directly.
