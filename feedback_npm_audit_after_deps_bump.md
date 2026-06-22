---
name: feedback_npm_audit_after_deps_bump
description: After each deps-bump wave run npm audit; document unfixable findings in TECH_DEBT.md with revisit triggers; never `npm audit fix --force`
metadata:
  node_type: memory
  type: feedback
  originSessionId: b9dc75ef-7af9-4483-9045-2c458bbc279e
---

After applying a deps-bump wave, run `npm audit`. Document unfixable findings (0 high/critical, but moderate chains blocked upstream) in `TECH_DEBT.md` with concrete revisit triggers. Never run `npm audit fix --force` — it can downgrade a package (e.g. `@hey-api/openapi-ts`) to dodge a build-time-only or low-exploitability advisory, which is a net negative. Non-force `audit fix` is a confirmed no-op when upstream hasn't shipped a fix; accept and document instead.

**Why:** the user followed a bump wave with *"lets follow up with checking npm audit"* — treat the audit as a standing follow-on step of the deps-bump flow, not a one-off.

**How to apply:** fold `npm audit` into the `deps-bump` flow after the bumps land; record the unfixable chains (and what would unblock them) in `TECH_DEBT.md` per [[feedback_match_artifact_format_to_project]]. Related: [[feedback_dependency_pinning]], [[feedback_never_override_min_release_age]], [[feedback_npm_ci_for_installs]].
