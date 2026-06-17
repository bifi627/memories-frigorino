---
name: feedback_worktree_lsp_noise
description: "User prefers running subagent-driven plan execution in the main checkout, not a worktree, to avoid LSP false-positive noise."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 7acc5c1c-6790-4ab7-9f32-ac98b12735a2
---

During subagent-driven plan execution, the user asked to "move off the worktree to avoid the LSP noise". Git worktrees created under `.claude/worktrees/` are NOT loaded into the IDE's LSP workspace, so every edit triggers `<new-diagnostics>` system-reminders full of false-positive `CS0246` "type/namespace not found" errors (FluentResults, Xunit, project nav types) — the worktree compiles fine via `dotnet build`/`dotnet test`, the LSP just can't resolve its project references. This noise pollutes the agent loop turn after turn.

**Why:** The LSP only indexes the primary repo path (`C:\Repositories\frigorino`). Worktree paths produce phantom diagnostics that are pure noise and can mislead reviews.

**How to apply:** For multi-task plan execution where you'll be editing+verifying repeatedly, work directly in the main checkout on the feature branch instead of an EnterWorktree worktree. To migrate an in-progress worktree branch back: `ExitWorktree keep` → `git worktree remove [--force] <path>` (deregisters worktree, keeps the branch ref + commits; dir-delete may hit a lock, that's a harmless gitignored leftover) → `git checkout <branch>` in the main repo. This refines [[feedback_worktree_from_stage]] (still branch off stage; just don't isolate in a worktree when LSP feedback matters). LSP diagnostics from a real in-workspace path ARE trustworthy — only worktree-path diagnostics are noise.
