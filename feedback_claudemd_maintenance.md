---
name: feedback_claudemd_maintenance
description: "How to keep CLAUDE.md in sync — incremental audit-and-update, never delete-and-reinit"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b3c9f2c2-bc2b-4cb5-ab5d-2232931e875d
---

Keep CLAUDE.md current via **incremental audit-and-update**, not delete-and-`/init`-from-scratch.

**Why:** CLAUDE.md holds hand-curated institutional knowledge a fresh scan can't reconstruct (the "why" behind decisions, "we tried X and reverted it" notes, "trust doc Y over Z" pointers). `/init` captures structure but wipes that. The user asked which approach was better and chose incremental after I recommended it.

**How to apply:** When CLAUDE.md drifts, audit each factual claim against the current tree (file pointers valid, line refs not shifted, framework/version, migration state, that named types/flags still exist) and propose a minimal diff — don't regenerate. Parallel read-only verification agents work well for breadth. Preserve verified-correct sections verbatim; only change what's stale. Stay terse (see [[feedback_claudemd_terse]], <200 lines). A complete `Write` rewrite is fine as the mechanism as long as correct content is carried over unchanged.
