---
name: feedback_ifelse_over_ternary
description: Prefer if/else over ternary for business logic with multi-part conditions; ternary only for quick simple comparisons
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e91aadef-f7bd-451f-b895-c66fcdd9536f
---

For business logic, prefer a normal `if/else` over the ternary `?:` operator — especially when the condition has multiple parts (`&&`/`||` chains) or the branches build non-trivial values. Ternary is fine for quick, simple comparisons.

**Why:** Big conditions inside a ternary hurt readability; the user finds multi-condition ternaries hard to read.

**How to apply:** When assigning a value based on a compound condition (e.g. `X? a = cond1 && cond2 && cond3 ? Analyze(...) : null;`), expand to `X? a = null; if (cond1 && cond2 && cond3) { a = Analyze(...); }`. Likewise expand ternary-returning dispatch (e.g. route selection) into `if (...) { return ...; } return ...;`. Reserve `?:` for short single-condition expressions.

Also relates to [[feedback_csharp_braces]] (always block braces).
