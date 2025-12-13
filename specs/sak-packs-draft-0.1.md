# SAK Packs (Draft 0.1)

**Boundary:** This document defines distributable bundles ("packs") that reference contracts and artifacts. It does **not** redefine schemas or operator semantics.

## 1. Purpose

A **Pack** is a versioned, distributable, and composable delivery boundary for a set of related specifications, contracts, and/or content.

Packs enable:
- independent versioning,
- dependency declaration,
- compatibility control,
- repeatable installation/composition.

## 2. Pack Model (Shared)

A pack manifest MUST declare:

- `pack_id` (stable name)
- `version`
- `pack_type`: {artifact_pack, operator_pack, extension_pack, ...}
- `compatibility`:
  - required core spec versions (e.g., Self Model Spec, SD, DE, SAK Contracts)
- `dependencies[]` (other packs by id/version constraints)
- `contents[]`:
  - references to artifact kinds/contracts and/or operator contracts
- `license` (optional)
- `notes` (optional)

## 3. Core Packs

### 3.1 Artifact Pack – Core

**Intent:** Define the minimum portable artifact set SAK supports.

Contents MUST include references to:
- SD_LOG (by reference to SD spec)
- RD (by reference to DE spec)
- SELF_MODEL (by reference to `self-model-spec.md`)
- Common metadata requirements (by reference to `sak-artifacts-and-operators.md`)

### 3.2 Operator Pack – Core

**Intent:** Define the minimum operator contract set SAK supports.

Contents MUST include references to:
- `compile_self_model`
- `enrich_self_model`
- `merge_patch`
- `rollback_version`
- `export_self_model`

## 4. Extension Packs (Productizable Unit)

An **Extension Pack** is a distributable unit that provides one or more Extensions (Enrich.Extension content) intended to be composed into a Self Model at runtime/export time.

An Extension Pack manifest MUST additionally declare:

- `declared_scopes[]`: where the Extensions are intended to apply
- `precedence_hint` (optional, informative)
- `conflict_notes` (optional, informative)
- `recommended_export_scopes[]` (optional)

> Extension Packs are content-oriented. They SHOULD avoid embedding platform-specific prompt syntax; adapters belong elsewhere.

## 5. Pack Versioning Rules (Minimal)

- Pack version bumps MUST indicate compatibility intent:
  - Breaking changes → major
  - Additive changes → minor
  - Fixes/metadata → patch
- A pack MUST NOT silently change semantics under the same version.

## 6. Non-Goals

- Defining a universal registry or marketplace protocol.
- Defining how packs are signed or verified (can be layered later).
