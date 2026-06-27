---
name: reference_i18n_namespace_two_files
description: Adding a new top-level i18n namespace to the Frigorino SPA needs TWO edits — the JSON resources AND the typed-key augmentation in src/types/i18next.d.ts — or tsc rejects every t("newNs.*") call.
metadata:
  node_type: memory
  type: reference
  originSessionId: 08e28a0b-5ffa-4f51-9cc7-edd31bab2b0d
---

The ClientApp's i18next is strictly typed: `t()` keys are validated against a hand-maintained augmentation at `Application/Frigorino.Web/ClientApp/src/types/i18next.d.ts` (a `declare module "i18next"` block whose `resources.translation` lists each top-level namespace as `<ns>: Record<string, string>`). This type is **not** derived from the JSON — it's maintained by hand.

**How to apply:** when adding a brand-new top-level namespace (e.g. `copyToList`), edit THREE files, not two: `public/locales/en/translation.json`, `public/locales/de/translation.json`, **and** `src/types/i18next.d.ts` (add `copyToList: Record<string, string>;`). Skip the `.d.ts` edit and `npm run tsc` fails with `"copyToList.x" is not assignable to <union of existing prefixes>` on every usage. Adding *keys to an existing* namespace needs only the JSON files (the value type is `Record<string, string>`, so individual keys aren't typed). Caught during the recipe copy-to-list feature. See [[feedback_test_assertions_no_translated_text]].

**Caveat — the JSON itself is not tsc-validated.** `tsc` checks only the `.d.ts` namespace registration, never the locale JSON's syntax or key existence; the JSON loads at **runtime**. A malformed or missing-key `translation.json` passes `npm run tsc` clean and only breaks in the running app. After editing any locale JSON, parse-check it explicitly — `node -e "JSON.parse(require('fs').readFileSync('public/locales/en/translation.json','utf8'))"` (and the `de` file) — and confirm expected key counts.
