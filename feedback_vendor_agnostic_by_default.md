---
name: feedback_vendor_agnostic_by_default
description: "When introducing an external vendor (AI/OCR/push/SMS/etc.), do vendor research first and design a vendor-agnostic interface — don't bake vendor specifics into entity names, configs, or DI registrations."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: cd9dac8f-37b5-4802-aca0-c92ec3c30e14
---

When a feature requires an external third-party service (AI/LLM, OCR, push-notification provider, SMS, email, payment, etc.), default to:

1. **Define the abstraction first** — `IClassificationService`, `IPushSender`, etc., in `Frigorino.Domain.Interfaces`. Method signatures express what the *feature* needs, not what the vendor offers.
2. **Vendor-neutral names everywhere user-visible-in-code** — config sections (`Classifier:*` not `OpenAi:*`), DI extension methods (`AddClassifier()` not `AddOpenAi()`), settings classes, env vars.
3. **One implementation registration**, swappable. Vendor-specific naming is acceptable inside the implementation file itself (`OpenAiClassificationService.cs`) but doesn't leak.
4. **Vendor selection is its own task.** Spec should list it explicitly — side-by-side on cost-per-call, accuracy, prompt-tuning needs, latency, EU data residency, offline/local-LLM viability — before committing to one. Don't assume "OpenAI because everyone uses OpenAI."

**Why:** The user explicitly flagged "don't lock in OpenAI, we have to research other vendors too" when reviewing the classifier-for-perishables design. Vendor lock-in is reversible at high cost if the abstraction was missed; cheap if it was designed in. Also — pricing and EU data residency on AI vendors shift quarterly; the right pick today may not be the right pick in six months. Same reasoning extends to push providers, OCR engines, email senders, etc.

**How to apply:** Any time a feature spec introduces a third-party paid/network service, the spec must (a) name the interface, (b) flag vendor-selection as an explicit task, (c) keep config / DI / settings vendor-neutral. If a future PR pattern-matches against "drop in service X" without these, push back.

**Exception — don't wrap when the vendor type IS the abstraction.** When the library already exposes a generic, well-shaped, swappable interface whose ergonomics are the point, inject it directly instead of adding a redundant `IXxx` wrapper. Confirmed 2026-05-25 on the Hangfire spec: the user chose to inject Hangfire's `IBackgroundJobClient` directly into producer slices rather than a vendor-neutral `IBackgroundJobQueue`, because the wrapper would shed the strongly-typed `Enqueue<TJob>(j => j.ExecuteAsync(...))` expression capture (the whole reason to use it) and add nothing. Distinguishing test: a *vendor-branded service tied to vendor specifics* (OpenAI completions, a particular OCR API shape) → wrap it; a *generic infrastructure primitive* the library happens to provide (queue client, logger, cache) → use it directly. The rule above is about the former.

Related: [[project_railway_hosting]] (Railway as the host is locked-in for hosting; this rule is about *external integrations from* the app, not the platform we deploy on).
