---
name: feedback_remove_tracking_items_when_done
description: Delete IDEAS.md / TECH_DEBT.md / bug entries once the work they describe is finished
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a022abee-1f5a-4586-9e3a-69ff8d76e0ce
---

When work tracked by an entry in `IDEAS.md`, `TECH_DEBT.md`, or a bug list is completed, delete that entry as part of the finishing step — don't leave shipped items reading as forward-looking/open.

**Why:** these files are meant to reflect outstanding work; stale "done" entries make the backlog lie and force re-investigation (like the IDEAS↔specs reconciliation we just did). The code/git history is the record of what shipped, not the idea file.

**How to apply:** at the end of implementing a feature or fix, remove its tracking entry in the same change. For multi-cycle items, delete only the completed cycle's portion and leave the deferred remainder. Mirrors [[feedback_remove_dead_code]] (delete now, not later) and [[feedback_match_artifact_format_to_project]].
