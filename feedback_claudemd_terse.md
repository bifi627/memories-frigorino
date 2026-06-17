---
name: feedback_claudemd_terse
description: "Keep CLAUDE.md updates terse — plant information with little overhead, not multi-paragraph expansions"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 62909c56-e779-4028-84af-0b2121af64db
---

When updating `CLAUDE.md` (and similar always-loaded context docs), keep edits tight. Default to a paragraph or two of dense prose with file-path pointers, not multi-section walkthroughs with examples, motivation paragraphs, and bullet enumerations.

**Why:** User said "dont bloat claude.md too much plant this information with little overhead" — explicit pushback against a 30-line section that re-explained things the linked files already cover. CLAUDE.md is loaded into every conversation; every extra line is a permanent context cost across all future sessions.

**How to apply:**
- One short paragraph stating what the thing is + the entry point (script name, file path, function).
- Optional one-line bullets only when there are genuinely independent items.
- Skip: motivation paragraphs, "how it works" walkthroughs, healthcheck/edge-case footnotes, repeated callouts of decisions already in the linked code.
- Trust file pointers — the implementation file explains itself; CLAUDE.md just needs to make it discoverable.
- Skill descriptions and TECH_DEBT.md entries are the right place for detailed explanations, not CLAUDE.md.
