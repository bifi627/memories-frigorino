---
name: reference_inmemory_provider_rejects_collection_converter
description: EF array/collection value-converter columns must be gated to a relational provider or they break every Frigorino.Test InMemory test at model-build.
metadata: 
  node_type: memory
  type: reference
  originSessionId: e019bb8c-5e4a-48d5-af57-d91d754a596c
---

A property mapped with a value converter that targets an **array/collection** store type (e.g. `Recipe.Tags`: `List<RecipeTag>` ⇄ `int[]` + `HasColumnType("integer[]")`) throws under the EF **InMemory** provider that `Frigorino.Test`'s `TestApplicationDbContext` uses:

> `System.ArgumentException : Cannot compose converter from 'List<RecipeTag>' to 'int[]' with converter from 'IEnumerable<int>' to 'string' ...`

It fails at **model-build time**, so it takes down EVERY InMemory-backed test at once (here: 59 of 551), not just tag tests — and the symptom is identical across all of them. Scalar `HasColumnType("numeric")/("text")` + `UseCollation("C")` do NOT break InMemory (they don't change the CLR mapping); a value **converter** does.

**Fix pattern:** apply the Npgsql-specific collection mapping provider-conditionally in `ApplicationDbContext.OnModelCreating` behind `if (Database.IsRelational())`, and keep it OUT of the provider-agnostic `IEntityTypeConfiguration` block (which `ApplyConfigurationsFromAssembly` runs for every provider). InMemory then falls back to EF's native primitive-collection mapping. Verify both sides: `Frigorino.Test` (InMemory) green AND the Postgres round-trip IT green. Committed in the recipe-tags feature (`b8915c0`). Related: [[feedback_db_tests_testcontainers]], [[reference_ef_isnull_pruned_on_required_column]], [[feedback_flat_db_schema]].
