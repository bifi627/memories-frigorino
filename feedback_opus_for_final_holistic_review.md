---
name: feedback_opus_for_final_holistic_review
description: "In subagent-driven plan execution, always use the opus model for the end-of-phase final holistic review"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5aaf5240-ab70-4919-b710-3ee635819aec
---

When running superpowers:subagent-driven-development, the per-task implementer + spec/quality reviewers may use cheaper models (sonnet) for mechanical tasks, but the **final holistic / end-of-phase review must always run on opus**.

**Why:** A sonnet holistic review of Phase A reported "COHERENT — no issues" but under-weighted a subtle EF Core persistence bug (HasDefaultValue → ValueGeneratedOnAdd sentinel-skip dropping CLR-default values on INSERT). Cross-task coherence + subtle correctness is exactly where the strongest model earns its cost.

**How to apply:** Pass `model: "opus"` to the Agent call for the final holistic review at the end of each session/phase. Per-task reviews can stay on sonnet.
