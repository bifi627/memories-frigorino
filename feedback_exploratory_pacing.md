---
name: feedback_exploratory_pacing
description: "When user is exploring options (sounds, libraries, approaches), don't bulk-execute — answer the immediate question and wait for their pick before editing files"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 63a9c902-fb59-449b-b6d6-1b3d5e1577ba
---

When the user is in "exploring options" mode — comparing sounds, libraries, approaches, file formats, etc. — do NOT proactively generate files, edit configs, or chain steps ahead of their explicit pick. Answer the immediate question, then stop.

**Why:** Twice in one session the user redirected me mid-execution: first "only let me know how it works and where to add" when I was about to inspect Windows .wav files, then "lets first try out the windows sounds, give me the folder name again please" when I started generating custom .wav files. They want to evaluate options interactively, not have me commit to one prematurely.

**How to apply:** Cues that we're in exploration mode — "can we...", "what about...", "let me know how it works", "lets first try X". In that mode: answer the literal question (path, command, name), offer next steps as a question, and wait. Save the proactive setup (file generation, config edits, dependency installs) for after they say "do it" / "go ahead" / "use X". This is opposite-of-default: normally chain steps; here, single-step until told otherwise. Related: [[feedback_prefer_bash]] is about *tool* choice; this is about *pacing*.
