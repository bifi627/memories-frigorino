---
name: feedback_csharp_braces
description: "Always use block-bodied {} in C# — no single-line if/namespace, no expression-bodied (=>) methods"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 27209bce-a272-451e-b461-65540827dc7e
---

**Rule:** Use block style `{}` for all C# bodies — conditions, namespaces, and methods. Never use single-line if/for/namespace syntax. Do not use expression-bodied member syntax (`=>`) for methods; use a `{ return ...; }` block instead.

**Why:** Consistency and readability — avoids mixing styles in the codebase and reduces diff noise when single-line statements or one-liner returns later expand. User has corrected this for both `if`/`namespace` shortcuts and expression-bodied methods.

**How to apply:** When writing or editing C# code, always include the opening and closing braces on separate lines, even for single-statement bodies. Applies to if/else, loops, try/catch, namespace declarations, AND methods (including DTO converters, helpers, and pass-through methods). Auto-properties (`{ get; set; }`) and lambdas remain unchanged.
