---
name: feedback_prettier_in_verification
description: Frontend verification flow must include a prettier check alongside lint and tsc; CI enforces it via prettier:check
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a309d2e8-e6f3-4189-a5f7-e43d29d77784
---

Always include prettier in the frontend verification flow, alongside `npm run lint` and `npm run tsc`. Locally run `npm run prettier` (which is `prettier --write .`) to apply formatting; CI uses `npm run prettier:check` (`prettier --check .`) as a quality guard that fails when formatting drifts.

**Why:** The user wants formatting enforced automatically rather than relying on manual discipline, so style stays consistent across the codebase and PRs.

**How to apply:** When verifying frontend changes in `Application/Frigorino.Web/ClientApp`, run lint + tsc + prettier. Use `--write` locally so you can commit the formatted result; never put `--write` in CI (it modifies files and exits 0, so it can't guard). The CI step lives in `.github/workflows/ci.yml` under the Frontend job, right after Lint. Both scripts are defined in `ClientApp/package.json`. See [[feedback_npm_scripts]] and [[feedback_scope_verification_by_confidence]].
