---
name: feedback_flat_db_schema
description: Avoid EF entity inheritance (TPH/TPT); model variants as a single flat table with a plain enum/type column + nullable columns
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 07a2c775-81ec-4183-9d38-02c8572dbb7f
---

When modeling entity variants (e.g. a ListItem that can be text / image / document), use a **single flat table** with a plain `Type` enum column and nullable columns for the variant-specific fields. Do NOT use EF Core inheritance (Table-Per-Hierarchy discriminators or Table-Per-Type joined tables) or a `Derived : Base` class hierarchy.

**Why:** The user has had bad past experiences where entity inheritance produced complicated, hard-to-maintain database schemas. They explicitly want the DB model kept as simple as possible.

**How to apply:** Prefer one table + nullable columns + a plain discriminator column the app reads. Keep read projections join-free where possible. The `Type` column should be an ordinary value the application branches on, not wired into EF's inheritance mapping. Balance simplicity against not over-engineering — flat-but-clear over clever hierarchies. Relates to [[feedback_avoid_unnecessary_migrations]].
