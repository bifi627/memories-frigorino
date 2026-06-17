---
name: feedback_match_artifact_format_to_project
description: "When producing a design / brainstorm artifact, match the project's existing format conventions (IDEAS.md, TECH_DEBT.md) for length and depth — don't default to full multi-section specs with code blocks."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: cd9dac8f-37b5-4802-aca0-c92ec3c30e14
---

When a brainstorming or design session produces an artifact (file in the repo), default to **matching the existing project format** for length and depth, not to a full multi-section spec.

In Frigorino specifically: `IDEAS.md` is the running list of forward-looking enhancements — entries are short (Title / Why / Sketch / Impact), one paragraph or a small bullet list per section, no code blocks. Detail belongs in implementation plans later, not in the idea capture.

Same shape: `TECH_DEBT.md` for deferred issues.

**Why:** The user pushed back ("way too detailed for now") after I walked through Section 1 (architecture overview) and Section 2 (data model with C# classes, indexes, migration shape, normalization rules, denormalization rationale) of a brainstormed feature. The `superpowers:brainstorming` skill's section-by-section flow naturally produces full design specs, which is the wrong artifact when the project already has a lightweight "ideas" capture file. The right artifact for "future ideas worth remembering" is a short IDEAS.md entry, not a 500-line design doc.

**How to apply:**
1. Before writing artifact content, look for project conventions (IDEAS.md, TECH_DEBT.md, ADRs, RFC folder, decision logs). Match their format.
2. In the brainstorming flow itself, keep IN-CONVERSATION text high-level (what & why). Defer code-level detail (entities, columns, migrations, signatures) to the artifact file — and even there, only if the project's artifact format expects it.
3. Calibrate per signal: if the user picks "full design spec", you can write more in the artifact, but still keep CONVERSATION lighter — they can read the file.
4. If unsure, offer artifact-shape options early ("IDEAS.md entry vs. full design doc vs. just talk it through"). Don't assume "design spec" means "every architectural detail."

Related: [[feedback_exploratory_pacing]] (don't chain steps ahead of user's pick), [[feedback_claudemd_terse]] (keep docs that load every session terse).
