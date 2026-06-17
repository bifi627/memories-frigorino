---
name: reference_railway_grid_timestamptz_blank
description: "Railway Postgres data-grid shows timestamptz columns as blank — display quirk, not NULL/missing data"
metadata: 
  node_type: memory
  type: reference
  originSessionId: aa9c2e83-07e6-40c9-9beb-b8cfb86c134c
---

Railway's Postgres "Data" table/grid view does NOT render `timestamp with time zone` columns — they appear blank in the list, looking identical to genuinely-NULL columns. The values are fine: open the **row detail panel** (or query the column directly) and the real timestamps show.

Diagnostic that confirms it's the quirk and not bad data: `SELECT COUNT(*) FROM "ListItems" WHERE "UpdatedAt" IS NULL` returns 0 while the grid shows blank `CreatedAt`/`UpdatedAt`. To rule out a genuine default/`0001-01-01` (`DateTime.MinValue`) row, query `WHERE "UpdatedAt" < '2000-01-01'`, since that is a real value `IS NULL` won't catch.

Burned investigation time on 2026-06-05 chasing a phantom "legacy items with empty UpdatedAt → CheckedItemPurge data loss" bug that didn't exist. Related: `ListItem.CreatedAt/UpdatedAt` are non-nullable (`NOT NULL`) and `DeleteInactiveItems` only purges `Status=true` checked items past retention or `IsActive=false` soft-deletes — unchecked active items are retained forever by design.
