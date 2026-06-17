---
name: project_delete_inactive_items_contract
description: "Every new soft-delete (IsActive) aggregate in Frigorino must be added to the DeleteInactiveItems maintenance task AND given a Cascade parent FK, or its soft-deleted rows leak forever — not compiler- or test-enforced."
metadata:
  node_type: memory
  type: project
  originSessionId: 7acc5c1c-6790-4ab7-9f32-ac98b12735a2
---

`DeleteInactiveItems` (the startup maintenance task) is the *only* thing that hard-deletes soft-deleted children — Household/List/Inventory deletes are soft-only, and this task is the actual cascade that reaps the `IsActive = false` rows (idempotent, runs on every cold start). It does **not** auto-discover entity types: each aggregate has a hand-written `ExecuteDeleteAsync` clause.

**Why:** shipping a new soft-deletable aggregate (e.g. Recipes) without touching this task means its soft-deleted rows are never reaped — they leak permanently. Nothing in the compiler or the test suite catches the omission; it surfaced only via a spec-review agent reasoning about the cascade.

**How to apply:** when adding a new aggregate with `IsActive` soft-delete + household cascade, do BOTH: (1) add an `ExecuteDeleteAsync` clause for it in `DeleteInactiveItems.cs`, and (2) set its parent FK `OnDelete` to `Cascade` in the EF config so the DB cascade matches the sibling aggregates (Inventory is the reference). Omitting either leaks rows. Related: [[project_listitem_slice_classification_contract]] (another "a new slice/aggregate must also wire X" contract with no central enforcement).
