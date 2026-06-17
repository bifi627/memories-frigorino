---
name: feedback_opus_for_final_holistic_review
description: "Always use the opus model for the end-of-phase final holistic review — in BOTH subagent-driven and inline (executing-plans) execution; run it unprompted, don't wait to be asked"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5aaf5240-ab70-4919-b710-3ee635819aec
---

When running superpowers:subagent-driven-development, the per-task implementer + spec/quality reviewers may use cheaper models (sonnet) for mechanical tasks, but the **final holistic / end-of-phase review must always run on opus**.

**Why:** A sonnet holistic review of Phase A reported "COHERENT — no issues" but under-weighted a subtle EF Core persistence bug (HasDefaultValue → ValueGeneratedOnAdd sentinel-skip dropping CLR-default values on INSERT). Cross-task coherence + subtle correctness is exactly where the strongest model earns its cost.

**How to apply:** Pass `model: "opus"` to the Agent call for the final holistic review at the end of each session/phase. Per-task reviews can stay on sonnet.

**Scope — inline execution too (added 2026-06-16, session c162afc9):** This is not limited to `subagent-driven-development`. During an **inline** (`executing-plans`) run the user explicitly prompted *"did you run holistic review agent? If not run please"* — i.e. they expect the end-of-phase opus holistic review in inline mode as well, run proactively without being asked. Treat it as a standing step for any multi-task plan execution, both modes.
