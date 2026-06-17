---
name: reference_ef_isnull_pruned_on_required_column
description: "EF Core prunes LINQ `x == null` to WHERE FALSE when the model maps the column required, even if the DB column is actually nullable"
metadata: 
  node_type: memory
  type: reference
  originSessionId: d99fcddb-216c-44d4-9273-52578a456c06
---

EF Core's `SqlNullabilityProcessor` optimizes a LINQ `IS NULL` predicate to `WHERE FALSE` when the **model** maps that column non-nullable (`.IsRequired()` / non-nullable CLR type) — regardless of the column's real DB nullability. `EF.Property<string?>(e, "Col") == null` does NOT defeat this: the cast changes only the expression's CLR type, not the mapped `IProperty.IsNullable`. Confirmed via `.ToQueryString()` → `... WHERE FALSE`.

This bit the fractional-index expand migration: `RankBackfill`'s "needs backfill" guard `AnyAsync(Rank == null)` always returned false → backfill silently never ran (`Rank` is `.IsRequired()` in the model but the column was added nullable for the expand phase). Pre-existing rows would have kept NULL ranks on deploy.

**Fix used:** raw SQL for the guard — `db.Database.SqlQueryRaw<int>("SELECT COUNT(*)::int AS \"Value\" FROM \"ListItems\" WHERE \"Rank\" IS NULL")` — which bypasses the optimizer. **How to verify:** `query.ToQueryString()` shows the real SQL with no DB connection needed.

**Watch for it** in any expand/contract migration here (model-required + temporarily-nullable column). Integration suites that only create rows via the API won't catch it — the rows are never null; needs a fill-path test that NULLs ranks via raw SQL.

**Status (verified 2026-06-16):** the original example is gone — the `Rank` NOT-NULL contract migration has since landed, the `RankBackfill` maintenance task was removed, and no `SqlQueryRaw` guard remains in the codebase. The EF behavior itself is durable and will recur on the next expand/contract migration; keep this as a watch-for. Historical origin: the fractional-index work on PR #94.
