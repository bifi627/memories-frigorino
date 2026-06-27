---
name: project_inventory_items_individual_lots
description: "Inventory items are individual lots — never dedup/merge on add or promote; each purchase is its own row with its own expiry date"
metadata:
  node_type: memory
  type: project
  originSessionId: 145ef08f-2d3b-4d98-b59c-41a7195aa6ec
---

Inventory must **not** deduplicate or merge items on add or promote-to-inventory. Each purchase is an individual lot with its own expiry date — buy beef two days apart and you have two rows with separate expiries. Merging would destroy the per-lot expiry tracking that is the feature's core value; treat every inventory row as a unique item.

**Why:** stated as a deliberate product decision (not yet reflected in code) — "dedup on inventory should not be done because each item has an individual exporation date... i would treat them as individuals."

**How to apply:** when building promote/add flows, never collapse matching inventory rows. (List-item dedup is a separate, still-open topic — don't infer a list rule from this.)
