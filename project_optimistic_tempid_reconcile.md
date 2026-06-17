---
name: project_optimistic_tempid_reconcile
description: Optimistic item-create hooks MUST swap the Date.now() temp id for the real server id in onSuccess (mirror useCreateListItem).
metadata: 
  node_type: memory
  type: project
  originSessionId: 7acc5c1c-6790-4ab7-9f32-ac98b12735a2
---

Frigorino's optimistic `useCreateXItem` hooks seed the new row with `id: Date.now()`. The create `onSuccess` MUST reconcile that temp id to the real server item immediately — `setQueryData(key, old => old?.map(i => i.id === context.tempId ? data : i))` — not rely only on the debounced invalidation.

**Why:** if the user edits/reorders the row before the ~1s debounced refetch lands, the PUT/PATCH targets `/items/{Date.now()}`. `Date.now()` (~1.7e12) **overflows the `{itemId:int}` route constraint**, so the route doesn't match and the request falls through to `MapFallbackToFile("index.html")` — the SPA middleware throws on a non-GET → opaque **HTTP 500 with empty body**. (Diagnosing it is painful: server logs aren't surfaced in `dotnet test` output, and the 500 has no body — see `TECH_DEBT.md`.)

**How to apply:** when adding a new optimistic-create item hook, mirror `features/lists/items/useCreateListItem.ts` (which does the swap), NOT the pattern that only debounce-invalidates. Caught by the recipe e2e integration test (edit-immediately-after-add); fixed in `useCreateRecipeItem` (`cd5ec3d`) and the same latent bug in `useCreateInventoryItem` (`57e0ebc`). Relates to [[feedback_verbatim_plan_verify]] (manual/e2e verify is the net for plan-baked runtime bugs the plan told us to mirror the wrong sibling).
