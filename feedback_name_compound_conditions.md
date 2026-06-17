---
name: feedback_name_compound_conditions
description: Extract multi-part boolean conditions into a well-named variable instead of inlining them in if/while
metadata: 
  node_type: memory
  type: feedback
  originSessionId: e91aadef-f7bd-451f-b895-c66fcdd9536f
---

Extract a compound boolean condition (multiple `&&`/`||` clauses) out of an `if`/`while` into a local variable with a meaningful name that states the intent, then branch on the variable.

**Why:** A named condition documents *what the check means* (not just the mechanics) and reads far better than a long inline expression.

**How to apply:** e.g. `if (request.Text is not null && request.Quantity is null && request.ClearQuantity != true)` → `var textChangedWithoutQuantityIntent = request.Text is not null && request.Quantity is null && request.ClearQuantity != true; if (textChangedWithoutQuantityIntent) { ... }`.

**Watch the nullability trade-off:** pulling a `x is not null` clause behind a named bool loses C# flow-narrowing, so a later `Method(x)` may warn. Fix by widening the callee's parameter to nullable when it already null-guards internally (preferred) rather than re-adding a `!` operator.

Pairs with [[feedback_ifelse_over_ternary]] and [[feedback_csharp_braces]].
