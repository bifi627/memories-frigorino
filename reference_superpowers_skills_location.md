---
name: reference_superpowers_skills_location
description: Filesystem path to the installed superpowers plugin skills (SKILL.md sources)
metadata: 
  node_type: memory
  type: reference
  originSessionId: 0e142000-f7f5-42ee-8cbf-4e2da1bbc8f2
---

The superpowers plugin skills live on disk at:
`C:\Users\Dennis\.claude\plugins\cache\claude-plugins-official\superpowers\<version>\skills\<skill-name>\SKILL.md`

As of 2026-06-17 the newest version dir is `6.0.2` (older `6.0.0`/`5.1.0` plus a couple of SHA-named dirs also exist; prefer the highest semver one). It is the official `claude-plugins-official` marketplace cache — NOT under `plugins/repos/...` or any `superpowers-marketplace` path (those don't exist here).

14 flat skills (no nested category folders): brainstorming, dispatching-parallel-agents, executing-plans, finishing-a-development-branch, receiving-code-review, requesting-code-review, subagent-driven-development, systematic-debugging, test-driven-development, using-git-worktrees, using-superpowers, verification-before-completion, writing-plans, writing-skills.

Plugin root (`..\<version>\`) also has `commands/`, `hooks/`, `agents` config, `docs/`, `tests/`. To inspect a skill, Read the SKILL.md directly — do NOT use the Skill tool just to read it. Verify the version dir with a quick `ls` before hardcoding, since it bumps on plugin updates.
