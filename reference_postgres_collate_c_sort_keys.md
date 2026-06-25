---
name: reference_postgres_collate_c_sort_keys
description: "Postgres text columns ordered lexicographically (fractional-index / LexoRank rank keys) must use COLLATE \"C\" / UseCollation(\"C\"), or ORDER BY silently mis-sorts on case/punctuation."
metadata:
  node_type: memory
  type: reference
  originSessionId: d99fcddb-216c-44d4-9273-52578a456c06
---

A `text`/`varchar` column ordered lexicographically — e.g. `fractional-indexing` / LexoRank rank keys — MUST be created with byte collation: `UseCollation("C")` on the EF property and `COLLATE "C"` in the migration (and on any partial unique index over it). Postgres' default locale collation (`en_US.UTF-8`) sorts case and punctuation differently from the key alphabet, so `ORDER BY rank` (and `OrderBy(i => i.Rank)`) "almost works" then misorders on edge characters — a silent correctness bug, not a crash.

Surfaced and confirmed across **three sessions** during the List/Inventory integer-`SortOrder` → string-fractional-`Rank` refactor (brainstorm aa9c2e83 → design e5c737b7 → implementation d99fcddb / PR #94). The EF config is the canonical place; the migration reflects it via `collation: "C"` in `AddColumn`.

**Watch for it** on any new lexicographically-sorted string column here. Related: [[reference_ef_isnull_pruned_on_required_column]] (the other EF/Postgres gotcha from the same fractional-index work).
