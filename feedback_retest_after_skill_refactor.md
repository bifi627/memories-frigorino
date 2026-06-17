---
name: feedback_retest_after_skill_refactor
description: "When building/editing discipline skills via writing-skills TDD, re-run a pressure test after REFACTOR edits before claiming the skill is validated"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 815f8810-a746-4822-820f-0141f894de5c
---

When building or editing a discipline-enforcing skill via the writing-skills
TDD cycle (RED → GREEN → REFACTOR), always dispatch one more pressure-test
subagent **after** the REFACTOR edits, before marking the task complete or
telling the user the skill is validated/bulletproof. The loophole fix is itself
untested code.

**Why:** While building the `/retro` skill, I closed a behavioral-note loophole
with three edits and then declared the skill "bulletproof" / done with no
intervening verification run — the exact "claimed done without verifying"
failure mode that skill exists to catch. Surfaced by the retro on that session.

**How to apply:** After ANY behavior-changing edit to a skill (especially
discipline skills enforcing a rule), re-dispatch a verification subagent against
the edited version before claiming validation. Relatedly, discipline skills
warrant ≥2 distinct test scenarios including the boundary case (e.g. for a
"critique the user" skill, also test a genuinely clean session so it doesn't
manufacture fake findings). See [[feedback_scope_verification_by_confidence]].
