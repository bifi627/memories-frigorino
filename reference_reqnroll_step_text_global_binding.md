---
name: reference_reqnroll_step_text_global_binding
description: "Reqnroll step-text bindings are unique across the WHOLE IT assembly, not per feature/steps file; a clash throws AmbiguousBindingException at runtime"
metadata: 
  node_type: memory
  type: reference
  originSessionId: 96057bd1-e155-4863-a199-cfbc023067a7
---

A new `[When]`/`[Then]`/`[Given]` step phrase must be globally unique across ALL step classes in the `Frigorino.IntegrationTests` assembly — Reqnroll binds step text across the whole assembly, not within one feature or one steps file. A phrase that collides with a binding defined in an unrelated feature's steps (e.g. `RecipeAttachmentSteps`) throws `AmbiguousBindingException` at runtime (green build, red test run). Fix by phrasing the new step distinctly.

Bit the list document-attachments UI IT: the planned `"I attach a document with caption {string}"` collided with the existing recipe binding of the same text; disambiguated to `"I attach a document to the list with caption {string}"`.

Related: [[reference_reqnroll_keyword_sensitive]], [[reference_reqnroll_filter_silent_skip]].
