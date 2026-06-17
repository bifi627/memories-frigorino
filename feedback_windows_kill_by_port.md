---
name: feedback_windows_kill_by_port
description: "On Windows, TaskStop on a bash wrapper does not reap child node processes — kill by port instead"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 62909c56-e779-4028-84af-0b2121af64db
---

When you start a long-running node-based dev server (Vite, `npm run dev`, etc.) via `Bash(run_in_background: true)` on Windows, `TaskStop` only kills the bash wrapper. The child `cmd → node → node (vite)` tree survives as orphans and keeps holding the port (44375 for Vite, 5001 for the dotnet backend usually exits cleanly because dotnet itself handles the signal).

**Why:** Windows has no POSIX process-group cascade, so signals from a parent shell don't propagate to grandchild processes. POSIX systems do this via process groups + `kill -- -pgid`; Windows requires explicit tree-walk (`taskkill /T`) or per-PID cleanup.

**How to apply:** To kill a stuck dev server cleanly, prefer port-based lookup over PID files:
```powershell
Get-NetTCPConnection -LocalPort 44375 -State Listen -ErrorAction SilentlyContinue |
    Select-Object -ExpandProperty OwningProcess -Unique |
    ForEach-Object { Stop-Process -Id $_ -Force -ErrorAction SilentlyContinue }
```
Or from Bash: `taskkill //F //T //PID <pid>` once you've identified the listener.

When the Frigorino [`scripts/dev-down.ps1`](../../../../Repositories/frigorino/TECH_DEBT.md) lands, prefer that over ad-hoc kill commands — same logic, but it also runs `docker compose down`.
