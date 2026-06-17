---
name: feedback_clean_domain_separation
description: "User prefers separating distinct domain concepts into distinct objects/fields/flows over overloading one field with mode-dependent meaning (\"dirty invariants\") — e.g. a media caption must NOT reuse the text-item's main text field"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: feeeb742-c065-424d-9e72-ade9999aa069
---

When a single field or object would carry two different meanings depending on mode/type, the user prefers splitting it into separate concepts rather than overloading one ("I think its better to separate it and not do dirty invariants of this domain object").

**Why:** Overloaded fields force mode-conditional semantics and blur domain boundaries. Concrete case (composer redesign): a text item's primary `text` is its main content, but a media item's text is a *caption/description* — semantically different. The user explicitly did not want the caption routed into the composer's main text field; it should be its own input in its own surface.

**How to apply:** When you notice a field/object/component about to do double duty across variants, propose a clean separation (distinct field, distinct completion kind, distinct sub-flow owned by the relevant feature) before defaulting to a shared-field-plus-flag design. Sibling preference to [[feedback_flat_db_schema]] (no EF inheritance) — both are "keep the model clean and explicit, not clever."
