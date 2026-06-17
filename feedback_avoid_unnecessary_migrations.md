---
name: feedback_avoid_unnecessary_migrations
description: "When adding aggregate length constants, prefer matching the existing DB column width and skipping the EF migration over aligning aggregates with each other"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 14dc166f-9c58-444e-bbe8-20828013df9e
---

When growing a domain aggregate with length constants (`NameMaxLength`, `TextMaxLength`, etc.), prefer matching the existing DB column width over aligning the constant with a sibling aggregate's value if alignment would require a migration. Aggregate-level validation can be stricter than DB constraints; the constant doesn't need to be mirrored in `HasMaxLength(...)` on every column.

**Why:** The user explicitly pushed back on a generated migration for the Inventory migration round (Text widening 255→500 to "match ListItem") with "do we really need a migration?". Migrations add ops risk and the sibling-symmetry argument doesn't justify it for a hobby app — each aggregate's constraints can reflect its own entity's needs.

**How to apply:**
- Check the existing EF config / DB schema BEFORE picking a constant value.
- If the aggregate value matches the existing column, no migration is needed.
- If aggregate validation is stricter than the column allows (e.g. `Quantity` aggregate-max 100, column has no constraint), that's fine — the aggregate gates writes before they reach the DB. Don't add the column constraint just for symmetry.
- Only generate a migration when the schema change is actually load-bearing (e.g. NOT NULL column added, real index added, real semantic change).
