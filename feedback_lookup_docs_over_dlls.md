---
name: feedback_lookup_docs_over_dlls
description: "When investigating a NuGet/npm package's API surface (option names, method signatures, available properties), use context7 MCP first, then official docs via WebFetch, and only as a last resort grep/strings/ildasm on the compiled DLL. Priority order: context7 > http fetch > decompiling."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 540ec7f4-52a3-4f01-8a51-1ba0d5b7c3e7
---

When the build errors with "no definition for X" on a third-party package option, or you need to confirm an API surface, follow this strict priority order:

1. **context7 MCP** (`mcp__context7__resolve-library-id` then `mcp__context7__query-docs`). The user installed it specifically for this. It's curated, version-aware, and returns the canonical code snippet faster than fetching a README.
2. **WebFetch** of the official repo README, source file, or package page — when context7 has no entry or the result is too sparse.
3. **DLL inspection** (`strings`, `ildasm`, ILSpy) — reserve for the rare case where the option is internal-only AND both context7 and docs are silent. Even then, lead with a docs sweep first so you understand what *should* exist before checking what does.

**Why:** the user corrected this twice — first during the OTel rollout (preferred docs lookup over `strings` on the EF Core instrumentation DLL), then again when wiring Faro ("try using context7 before fetching docs by http or decompiling, context7 > http fetch > decompiling"). DLL inspection is noisy, slow, and easy to misread (compiler-generated names, internal-only members that look usable but aren't). WebFetch is faithful to the source but you have to know the right URL. context7 indexes the right docs with versioning and ranks by relevance.

**How to apply:**
- First move on any third-party API question: `resolve-library-id` with the library name + your question, then `query-docs` with the full question (not single words). Pick the result with high reputation and a snippet count > 0.
- If context7 returns nothing useful or the snippet count is suspiciously low (some libraries aren't indexed), fall back to `WebFetch` against:
  - the package's README on GitHub raw (e.g. `https://raw.githubusercontent.com/<org>/<repo>/main/...`)
  - the source file for the options class directly (`.../<OptionsClassName>.cs`)
  - the package page on nuget.org / npmjs.com or the official docs site
- For breaking-change confirmations: also fetch the package's CHANGELOG.md — option renames usually surface there. context7 sometimes catches these in its index too.
- Reserve DLL inspection (`strings`, `ildasm`, ILSpy) for the genuinely-internal-only case where both context7 and docs are silent.

Per-tool budget note: context7 caps at 3 calls per question (its own internal limit). If you need more lookups for the same question, switch to WebFetch rather than chaining context7 calls.
