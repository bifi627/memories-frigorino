---
name: reference_ef_computed_getters_not_translatable
description: "EF Core can't translate computed C# getters inside a .Select() DB projection; project the raw column expression the getter wraps instead"
metadata:
  node_type: memory
  type: reference
  originSessionId: 4c76f398-2a73-48b2-ae62-403f2f52fb2a
---

EF Core cannot translate computed C# getters (e.g. `Product.IsOverridden`, `Product.EffectiveCategory`) inside a `.Select()` projection that runs on the DB — it throws a translation error. Any query-side projection (read slice, backfill gap-selector) must use the raw column expression the getter wraps (e.g. `p.OverrideExpiryHandling != null` instead of `p.IsOverridden`). Computed getters are safe only on already-materialized entities.

Surfaced repeatedly in the product-classification override work (`ProductClassificationGaps`, `GetProducts`, `BackfillProductClassification`): the plan and Phase reviews all enforced "projections use column expressions — EF cannot translate computed properties."

Sibling EF gotcha: [[reference_ef_isnull_pruned_on_required_column]].
