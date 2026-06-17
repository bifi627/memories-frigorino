---
name: feedback_remove_dead_code
description: When discovering an unused component, file, function, or symbol, delete it as part of the current change rather than leaving it in place — even if it's outside the scope of the original task.
type: feedback
originSessionId: e95f7df5-e5df-45cd-b74c-f2146a93e340
---
If during a task I discover code that is genuinely unreferenced (e.g., a React component imported only by itself, an exported function with no callers, an obsolete service), I should delete it as part of the current change rather than leaving it for later.

**Why:** Dead code rots silently — it accumulates testids, broken imports, contradictory APIs (the `HouseholdSelector.tsx` vs `HouseholdSwitcher.tsx` situation), and most painfully, it misleads future readers (including future AI sessions) into editing the wrong file. Removing it the moment it's noticed prevents that drift. Git history covers the recovery case.

**How to apply:**
- Verify it's truly dead first: grep for the symbol across the project, ignoring its own definition file.
- Just delete the file/symbol. Don't leave a TODO, don't comment it out, don't add a deprecation notice. Use `git mv` or `rm` — the diff and commit message capture the rationale.
- Mention the deletion in the response so the user knows it happened, but don't ask permission for each instance — the user has standing instructions to remove dead code when noticed.
- This applies even outside the strict scope of the originating task (e.g., during a backend slice migration, deleting an orphaned frontend component is fair game).
- Don't conflate "currently unused" with "dead": some code is intentionally kept around for upcoming work or external consumers (public API, npm package surface). When ambiguous, ask.
