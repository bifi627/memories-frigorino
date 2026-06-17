---
name: project_agent_teams_sendmessage
description: "SendMessage subagent-resume is gated behind agent teams; enable via OS-level env var (NOT settings.json), restart required"
metadata: 
  node_type: memory
  type: project
  originSessionId: c41ab2d7-9694-4d97-8e8b-6e14c14694fc
---

The `SendMessage` tool (used to resume/continue a previously spawned subagent with its context intact) is gated behind agent teams and is **off by default**. Without it, the `subagent-driven-development` flow's "review found a bug → resume the original implementor to fix it" step fails with "SendMessage is not available" and falls back to fresh spawns. This is a known open Claude Code bug (github.com/anthropics/claude-code/issues/35240) — the `Agent` tool advertises the resume affordance (`use SendMessage with to: '<agentId>'`) even when the tool is gated off, so it's not misuse.

**How to enable (verified working on v2.1.161, Windows):**
- Set OS-level env var: `setx CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS 1`, then **fully restart Claude Code AND the launching terminal/IDE**.
- **`settings.json` `env` does NOT work** — the agent-teams gate is read at module init, before settings.json is parsed, so the var must be in `process.env` before launch.
- `--agent-teams` is an internal/hidden CLI flag (not in `--help`); the env var is the reliable path.

**Verify after restart:** `ToolSearch("select:SendMessage")` returns a real schema (when off it returns nothing); deferred-tool list also gains `SendMessage`/`TeamCreate`/`TeamDelete`. End-to-end: `Agent` spawn → `SendMessage(to: agentId, ...)` resumes from transcript with full prior context retained.

**Agent teams validated end-to-end (2026-06-03)** with a 3-lens review (TeamCreate → tasks → 3 named teammates self-claim → SendMessage findings → synthesize → shutdown_request → TeamDelete). Works in **in-process mode on Windows** (split panes need tmux/iTerm2 — NOT supported in VS Code/Windows Terminal). Observed quirks (match docs' experimental limits): tasks sometimes get assigned twice (harmless noise); a teammate mid-turn may need a re-sent shutdown_request. Teammates do NOT inherit lead's history — put full context in the spawn prompt. Use teams when workers must talk/challenge each other; use the `Workflow` tool for deterministic scripted fan-out (cheaper, but agents can't message each other); use plain subagents + `SendMessage` resume for the [[feedback_opus_for_final_holistic_review]] subagent-driven-development review→fix loop.

Related: [[feedback_opus_for_final_holistic_review]] (subagent-driven plan execution).
