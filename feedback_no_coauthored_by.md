---
name: feedback_no_coauthored_by
description: "Never add Co-Authored-By trailers (or any \"Generated with Claude\" lines) to git commits"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: a4a696cf-a517-4011-9f08-5e0d8ec5818b
---

Never add a `Co-Authored-By: Claude ...` trailer to commit messages. Do not add any "Generated with Claude Code" / robot-emoji attribution lines either. Commit messages should contain only the real content.

**Why:** The user explicitly rejected a commit that included the `Co-Authored-By` trailer and said "never do this to commits."

**How to apply:** Write commit messages with the subject + body only, no attribution trailers. Also set `includeCoAuthoredBy: false` in Claude Code settings so the default is off. Applies to every commit in every repo for this user.
