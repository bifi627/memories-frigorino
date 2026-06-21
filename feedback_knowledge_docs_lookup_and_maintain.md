---
name: feedback_knowledge_docs_lookup_and_maintain
description: "Read the matching knowledge/ doc before working a slice/feature, and update it in the same change when documented behavior shifts."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: d0ac5e3d-4478-4aae-b786-df7f20c4488d
---

When working on a vertical slice or feature, consult `knowledge/` first — start at `knowledge/README.md` (the index), then the matching doc. It splits into **pattern** docs (Backend_Architecture, Vertical_Slices, Frontend_Architecture, Frontend_Styling, API_Integration, Testing, Observability, Performance_Optimization), **capability** docs (AI_Classification, File_Storage, Firebase_Auth_Setup), and **feature** docs (Households, Members, Lists, Inventories, Recipes, Push_Notifications). A feature doc is the self-contained, citable unit (Overview · Domain · API surface · Key flows · Key decisions & rationale · Cross-feature touchpoints · Frontend · Links out); `Recipes.md` is the template exemplar.

When a change alters something a doc states — endpoints/verbs, domain constants, enum values, flows, decisions, cross-feature contracts — update the relevant doc as part of that change, not as a follow-up.

**Why:** `knowledge/` is the source-verified reference layer that specs/plans cite directly; stale docs silently mislead future work. Docs drift from code (a full audit found item `reorder` was documented as POST but is actually PATCH, list toggle is `PATCH /toggle-status` not `POST /toggle`) — verify claims against source, don't trust the doc blindly.

**How to apply:** read the feature doc before editing its slice; treat the doc edit as part of the implementation diff; keep edits minimal/incremental like CLAUDE.md ([[feedback_claudemd_maintenance]], [[feedback_claudemd_terse]]). Match the existing doc's section set/format ([[feedback_match_artifact_format_to_project]]). Remove tracker/idea entries when the work ships ([[feedback_remove_tracking_items_when_done]]).
