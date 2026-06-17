---
name: reference_reqnroll_filter_silent_skip
description: dotnet test --filter by FQN substring can silently skip a Reqnroll scenario (run shows green but never ran it); verify the matched count and filter on scenario-title words.
metadata: 
  node_type: memory
  type: reference
  originSessionId: a415f606-6127-443b-9933-b0bfb68f5b03
---

`dotnet test Application/Frigorino.IntegrationTests --filter "FullyQualifiedName~Reorder"` reported `Bestanden! erfolgreich: 7` but **never ran** the new `Re-order an inventory item…` feature — Reqnroll sanitizes the hyphen in the Feature/Scenario title (the generated class/method became `Re_order…`/`AddingAnInventoryItem…`), so the substring `Reorder` matched only pre-existing reorder-API tests. The green filtered run masked a scenario with a real seed bug (`decimal.Parse("2 l")` threw) that only surfaced in the full-suite run.

**How to apply:** after a filtered IT run, sanity-check the matched test count against what you expect — a single new scenario matching "7 tests" is a red flag it matched the wrong thing. The FQN is built from the sanitized **Feature title** (generated class) + sanitized **Scenario title** (method) — it is **NEVER** derived from the `.feature` file name. Build the filter from a distinctive word-run in the Feature/Scenario *title* and confirm the count (`gesamt: N`). A filtered run is not proof a specific scenario passed unless you confirmed it was actually selected.

**Repeat hit (recipe copy-to-list session, 2026-06-16):** first run used `--filter "FullyQualifiedName~CopyToList"` (the file is `CopyToList.Api.feature`) → "Kein Test entspricht" (zero matched), because the Feature title is "Copy recipe to shopping list (API)" → class `CopyRecipeToShoppingListAPIFeature`. Switching to `~CopyRecipeToShoppingList` matched. Lesson reinforced: **filter on the title, never the file name** — and on the *first* attempt, not after a wasted run.

Related: IT seed helper `TestApiClient.CreateInventoryItemAsync(…, quantity)` parses quantity as a plain number → Pieces (`decimal.Parse`), so `"2 l"` is invalid; pass `"2"`. See [[project_it_serves_clientapp_build]], [[feedback_verify_exit_code]].
