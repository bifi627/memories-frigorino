---
name: feedback_worktree_from_stage
description: "Git worktrees for this repo must always be created from the stage branch, not main/origin-default or current HEAD"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

Always create new git worktrees based on the `stage` branch.

**Why:** `stage` is the live-client UAT branch and is ahead of `main` (stage→main FF promotion — see [[project_branch_workflow]]). Feature work builds on stage's commits, so branching a worktree from `origin/main` (the Claude Code `worktree.baseRef: fresh` default) or from an arbitrary current HEAD would miss those commits.

**How to apply:** Do NOT rely on the `worktree.baseRef` setting (`fresh` = origin/main, `head` = current HEAD — neither guarantees stage). Instead create explicitly from stage, e.g. `git worktree add .claude/worktrees/<name> -b feat/<name> stage`, then switch the session in via `EnterWorktree path=<that path>`. The user rejected setting a global `baseRef: head` in settings.local.json.
