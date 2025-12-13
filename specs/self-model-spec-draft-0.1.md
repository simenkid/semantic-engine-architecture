# Self Model Specification (Draft 0.1)

**Boundary:** This document defines *semantic meaning* only. It does **not** define packaging/manifests, operator API signatures, or platform-specific adapters.

## 1. Purpose

A **Self Model** is a portable cognitive specification that can be applied to any LLM runtime to reduce response drift and increase interaction consistency across contexts.

This spec defines the minimum semantic structure:

- **Self Model = Core + Enrich**
- **Enrich = Profile + Extension**

## 2. Definitions

- **Core**: Stable, cross-context cognitive invariants (how the user tends to think and wants to be interacted with).
- **Enrich**: Optional enrichments that do **not** rewrite Core, but steer behavior under declared scopes.
- **Profile (Enrich.Profile)**: Long-term, cross-context preferences and quality criteria (e.g., values, style, interaction preferences).
- **Extension (Enrich.Extension)**: Context-/task-scoped lenses, principles, constraints, and evaluation criteria.
- **Scope**: A declared applicability region for an Enrich element. A scope can be defined by task class, domain, intent, or other runtime classifiers.

> **Note:** "Profile" here is a *user enrichment construct* (identity-adjacent, normative). It is distinct from descriptive "profiles" in analytics (e.g., statistical summaries).

## 3. Structure

A Self Model MUST be representable as:

- `core`: Core semantics (stable)
- `enrich`:
  - `profile`: Profile semantics (stable-ish, evolves slowly)
  - `extensions[]`: zero or more Extensions (scoped, pluggable)

Whether Profile/Extensions are embedded or referenced is a packaging decision (out of scope here). Semantically, the Self Model MUST behave as if they are available at evaluation time.

## 4. Semantics

### 4.1 Applicability (Scope Gating)

Each Enrich element MUST declare an applicability scope:

- **Profile** scope is typically broad (default/global), but MAY include exclusions.
- **Extension** MUST be scoped (never implicitly global).

At runtime, an Enrich element is **active** iff its scope matches the current task/context.

### 4.2 Priority and Arbitration

When multiple rules apply, the runtime MUST arbitrate deterministically.

Recommended default ordering (can be overridden by explicit policy):

1. **Core** invariants MUST NOT be violated.
2. Active **Extensions** apply next, ordered by:
   - explicit priority, then
   - specificity (more specific scope wins), then
   - recency (newer wins) if still tied.
3. **Profile** applies as a broad bias layer where not contradicted by Core or active Extensions.

### 4.3 Conflict Resolution

Conflicts occur when two active elements prescribe incompatible behavior.

The runtime MUST implement at least one of the following conflict strategies (and declare which it uses):

- **Hard constraints first**: treat "MUST/MUST NOT" as hard; treat "SHOULD" as soft.
- **Specificity wins**: more specific scope overrides broader scope.
- **Dual-output fallback** (recommended for user experience): produce
  - a "fast/strict" answer respecting stronger constraints, and
  - a "reflective/alternative" answer explaining trade-offs,
  when a single best action is not well-defined.

### 4.4 Non-Goals

This spec does not require:
- persona role-play or character simulation,
- platform-specific prompt formats,
- a particular storage/serialization format.

## 5. Evolution

### 5.1 Versioning Semantics

A Self Model MUST have a version identifier. Semantically:

- **Core** changes are rare and considered "major" meaning shifts.
- **Profile** changes are "minor" shifts.
- **Extension** addition/removal is "minor" or "patch" depending on scope impact.

(Concrete version numbering is a packaging policy.)

### 5.2 Patch / Merge / Rollback

A system MAY generate a **patch** that proposes changes to Profile and/or Extensions.

- Applying a patch MUST be explicit (no silent merges).
- A merge MUST be reversible (rollbackable) to a prior version.
- A diff MUST be explainable in human terms ("what changed and why").

## 6. Export Semantics (Platform-Agnostic)

An export is a projection of Self Model semantics into a target runtime format.

An export MUST:
- preserve Core invariants,
- include active/default Profile biases,
- include only those Extensions intended for the export scope (e.g., "creative work", "engineering decisions").

An export SHOULD:
- be minimal (avoid verbosity),
- be composable (allow multiple Extensions to be included),
- avoid platform lock-in.

## 7. Non-Goals

- Defining a universal taxonomy of task scopes.
- Defining how to infer Profile/Extensions from logs (belongs to analytics/compilers).
