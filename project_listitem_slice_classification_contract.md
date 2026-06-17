---
name: project_listitem_slice_classification_contract
description: "Any Frigorino slice that creates list items via list.AddItem must also fire IProductClassificationTrigger, or promote-to-inventory silently breaks for those items."
metadata: 
  node_type: memory
  type: project
  originSessionId: 08e28a0b-5ffa-4f51-9cc7-edd31bab2b0d
---

Creating a `ListItem` through `list.AddItem(...)` is only HALF the contract. The promote-to-inventory bar (`ToggleItemStatus` → looks up a `Product` by normalized name → `PromoteSuggestion.For`) only appears if a per-household `Product` catalog row exists. That row is created lazily by **`IProductClassificationTrigger.OnProductReferenced`**, which the normal `CreateItem` slice fires after save (via the quantity trigger, which chains classification). There is NO central enforcement — each slice must fire it itself.

**Why:** `CopyRecipeToList` initially only called `list.AddItem` + `SaveChanges`. Recipe items deliberately never create Product rows (`ExtractRecipeQuantityJob` is classification-free by design), so copied items had no catalog row → checking one off later never offered promote-to-inventory. User-reported bug, fixed on PR #130 (→stage).

**How to apply:** When writing a new slice that creates list items, decide BOTH:
1. **Classification** — if the item should be promotable to inventory, call `classificationTrigger.OnProductReferenced(householdId, cleanName)` after the save (route via `ItemTextRouter.Analyze` first and skip `SkipAi`, mirroring `NullQuantityExtractionTrigger`).
2. **Quantity extraction** — only when the quantity is NOT already supplied. If you already have a structured quantity (recipe copy, inventory→list re-order), do NOT run extraction — it would re-parse and could clobber it (see `CreateItem`'s re-order branch). Classification still runs; extraction does not.

Both triggers are DI-resolved to Null implementations when AI is disabled, so calling them is always safe. See [[feedback_verbatim_plan_verify]] (this gap survived the plan + static checks; only surfaced via the user exercising the real promote flow).
