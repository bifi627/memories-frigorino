---
name: feedback_context7_use_mcp
description: "Use the context7 MCP tools (mcp__context7__resolve-library-id, mcp__context7__query-docs) directly — never shell out to `npx ctx7@latest` or similar"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 07057de4-050e-4b97-8dd4-12af0b5dbbb9
---

For context7 doc lookups, always call the MCP tools directly:
- `mcp__context7__resolve-library-id` → pick the `/org/project` ID
- `mcp__context7__query-docs` → fetch with the user's full question

Do NOT invoke `npx ctx7@latest`, `npx -y @context7/cli`, or any shell wrapper.

**Why:** The user has the context7 MCP server configured with an API token already. The MCP path uses that token automatically; shelling out via `npx` bypasses it, requires interactive auth, and triggers permission prompts for no reason. The user explicitly called this out.

**How to apply:** Whenever the [[feedback_lookup_docs_over_dlls]] rule sends you to context7, reach for the MCP tools — not Bash/PowerShell. The npx CLI is not a fallback; if the MCP tools fail, fall through to WebFetch on the library's repo/README instead.
