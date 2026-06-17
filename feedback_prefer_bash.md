---
name: feedback_prefer_bash
description: Always use Bash tool instead of PowerShell for shell commands in this project
type: feedback
originSessionId: 55165bb7-4cc8-4231-ab53-617e5cfa1cfd
---
Always use the Bash tool instead of PowerShell for shell commands.

**Why:** Two reasons. (1) PowerShell executions require user approval each time; Bash commands can be allowlisted in settings.json and run without prompts. (2) The Windows PowerShell tool routes every command through Claude Code's "dangerous command detection" analyzer, which spawns `powershell.exe -EncodedCommand <base64>` (a nested-base64 AST parser). The user's **Bitdefender Advanced Threat Defense flags that analyzer as a "malicious command line" and blocks it** — a confirmed false positive (the analyzer never executes the inspected command; it only parses it). It trips especially when the analyzed command contains malware-ish tokens (EncodedCommand, FromBase64String, Invoke-Expression, curl+localhost, nested base64). Bash-tool commands don't go through this analyzer and run fine.

**How to apply:** Default to Bash for all shell operations (file listing, git, dotnet, npm, etc.). Only use PowerShell when Bash genuinely cannot (e.g. `Get-WinEvent` evtx parsing) — and warn the user it may trigger a Bitdefender block. Root-cause fix the user can apply: disable the opt-in PowerShell tool preview (revert to Git Bash routing) or add a Bitdefender ATD exception for the Claude Code process.
