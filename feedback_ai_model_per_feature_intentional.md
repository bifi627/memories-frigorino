---
name: feedback_ai_model_per_feature_intentional
description: "AI features' per-feature :Model values are deliberate; don't normalize one to match its siblings without confirming intent."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e019bb8c-5e4a-48d5-af57-d91d754a596c
---

Each AI feature in Frigorino has its own `Ai:<Feature>:Model` knob, and the values are chosen per-feature ON PURPOSE — they are NOT meant to converge. Recipe tag suggestion defaults to the **full** `gpt-5.4` while Classifier uses `gpt-5.4-mini` and QuantityExtractor uses `gpt-5.4-nano`.

**Why:** Tag suggestion is low-volume (one synchronous call per explicit button tap) and accuracy-sensitive, so the user deliberately spends more on a better model there — that per-feature configurability is the whole reason the knobs are separate. A reviewer flagged the "inconsistency" (`gpt-5.4` vs the mini/nano siblings + the DI fallback) and I changed appsettings to `gpt-5.4-mini` without confirming intent; the user reverted it — it was intentional.

**How to apply:** When config that looks inconsistent across sibling features is flagged, treat it as possibly intentional and ask before "fixing" it — especially model/cost/quality knobs. Documented in `knowledge/AI_Classification.md` (Recipe tag suggestion → "Model choice — full gpt-5.4, on purpose"). Related: [[feedback_flag_spec_contradictions_before_complying]], [[feedback_avoid_unnecessary_migrations]].
