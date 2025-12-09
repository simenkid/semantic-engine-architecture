# Discovery Engine Specification – Draft 0.1

> **Document ID:** discovery-engine-spec-draft-0.1  
> **Status:** Draft (v0.1, non-final)  
> **Intended Audience:** System architects, researchers, and implementers of SD/SEA-compatible systems.

---

## 0. Purpose and Scope

This document specifies the **Discovery Engine** for the SD/SEA stack.

- It defines how a Discovery Engine:
  - Observes **Semantic Dynamics (SD)**–style flows,
  - Infers a subject’s **Cognitive Rhythm**,
  - Emits **SEA-compatible artifacts**:
    - Cognitive Rhythm Descriptors (**CR-Descriptors**),
    - Candidate **RhythmSpecs** (for SEA Runtimes),
    - Optional **Profile hints** (for SEA Profiles).

- It is **separate from**:
  - SEA Core conformance requirements, and  
  - SD’s descriptive theory layer.

This specification is **normative** where explicitly marked (using MUST, SHOULD, MAY).  
Other content is informative/non-normative.

---

## 1. Position in the Stack

### 1.1 Relation to Semantic Dynamics (SD)

Semantic Dynamics (SD) provides a **descriptive vocabulary** for:

- mind-pops,  
- threads and branches,  
- dynamics phases (Emergence, Divergence, Resonance, Distillation, Stabilization, Overload, Rest),  
- tension types and trajectories.

The Discovery Engine:

- **Consumes** SD-structured logs (sessions, mind-pops, phases, tensions).  
- **Tags** micro-steps with Functional Tags aligned to SEA Functional Zones.  
- **Aggregates** these into CR-Descriptors.

### 1.2 Relation to SEA Core

SEA Core defines:

- A **Cognitive State space** (Core Functional Layers + Auxiliary Zones),
- **RhythmSpecs**: declarative descriptions of cognitive rhythms,
- **SEA Profiles**: bias and preference parameters.

The Discovery Engine:

- **Produces**:
  - RhythmSpecs that conform to SEA Core §6.2 semantics,
  - Profile hints that can be consumed by SEA Profiles,
  - without modifying SEA Core semantics.

SEA Core conformance **does not depend** on the existence of a Discovery Engine.  
Discovery Engine is an optional, SD-informed component.

---

## 2. Terminology

| Term | Definition |
|------|------------|
| **SD Log** | A structured record of one or more reflection / reasoning sessions represented in SD terms (mind-pops, threads, phases, Tension). |
| **Functional Tag** | A label from a fixed tag set (OBSERVE, FRAME, ABSTRACT, STRUCTURE, TENSION, DECIDE, META) describing the dominant cognitive role of a micro-step. |
| **Tagging Unit** | The smallest unit of analysis to which a Functional Tag is applied (e.g., an utterance, a clause, or a turn). |
| **Discovery Engine** | A system that consumes SD Logs, applies Functional Tagging, aggregates statistics into CR-Descriptors, and optionally compiles them into RhythmSpecs and Profile hints. |
| **CR-Descriptor (Cognitive Rhythm Descriptor)** | A structured summary of a subject’s typical functional distribution, transitions, and tension-driven patterns over time. |
| **RhythmSpec** | A SEA-compatible Rhythm Specification describing states, transitions, initial state, and termination conditions. |
| **Profile Hint** | A derived summary of tendencies (e.g., "frame-heavy, late decision") that can inform SEA Profiles. |
| **Subject** | The entity whose cognitive rhythm is being inferred (e.g., an individual human, a team, or a synthetic agent). |

Where appropriate, "MUST", "SHOULD", and "MAY" are interpreted as in RFC 2119.

---

## 3. High-Level Architecture

A Discovery Engine is conceptually divided into three main components:

1. **Tagging Layer**  
   - Input: SD Logs.  
   - Output: sequences of tagged micro-steps (Functional Tags + metadata).

2. **Aggregation Layer (CR-Descriptor Generator)**  
   - Input: tagged sequences over a window of sessions.  
   - Output: CR-Descriptors summarizing distributions, transitions, and tension correlations.

3. **Compilation Layer (Rhythm Compiler & Profile Extractor)**  
   - Input: CR-Descriptors.  
   - Output:
     - Candidate RhythmSpecs (SEA-compatible),
     - Optional Profile hints.

A concrete implementation MAY merge or split these components, but the **logical responsibilities** MUST still be identifiable.

---

## 4. Input Data Model – SD Logs

This section describes the **minimum structure** of SD Logs required by a Discovery Engine.

### 4.1 Session

A **Session** is a coherent reflection / reasoning episode.

Non-normative fields:

- `session_id` – unique identifier.
- `subject_id` – identifier of the subject.
- `timestamp_start`, `timestamp_end`.
- `context` – free-form or structured description (domain, task, stakes).
- `mind_pops` – ordered list of mind-pop entries.
- Optional: `phases`, `global_tension_summary`.

### 4.2 Mind-Pop

A **Mind-Pop** is a minimal SD unit of semantic emission (utterance, note, internal thought).

Non-normative fields:

- `mp_id` – unique identifier (within or across sessions).
- `role` – e.g., `human`, `engine`, `observer`.
- `text` – raw text content.
- `timestamp`.
- Optional: `thread_id`, `branch_id`.
- Optional: `sd_phase_label` – e.g., Emergence, Divergence, etc.
- Optional: `tension_snapshot` – Tension vector at or after this mind-pop.

### 4.3 Tension Information

Where available, Tension information SHOULD include:

- `tension_types` – e.g., `misframing`, `overload`, `boundary_conflict`, `value_clash`.  
- `tension_intensity` – numeric or ordinal measure.  
- `tension_notes` – free-text description.

**Normative requirement:**

- A Discovery Engine **MUST** be able to operate in a "Tension-light" mode where Tension labels are sparse or absent.  
  - In this case, driver correlations and condition-based transitions MAY be less precise.

---

## 5. Functional Tagging Layer

### 5.1 Functional Tag Set v0.1

The Discovery Engine MUST support at least the following Functional Tags:

- `OBSERVE` – Observe Zone (Auxiliary)  
  - gathering/revisiting inputs, reading, retrieving, tool calls.

- `FRAME` – Semantic Interpretation  
  - naming, reframing, clarifying intent and scope.

- `ABSTRACT` – Abstract Modeling  
  - extracting patterns, invariants, conceptual schemas.

- `STRUCTURE` – Layered Structuring  
  - decomposing into parts, layers, responsibilities, interfaces.

- `TENSION` – Tension Analysis  
  - surfacing conflicts, trade-offs, fog, overload, misalignment.

- `DECIDE` – Decision Synthesis  
  - committing to a direction, plan, structural choice, or next step.

- `META` – Meta-Reflect Zone (Auxiliary)  
  - reflecting on the process/rhythm, adjusting approach or granularity.

Additional tags MAY be defined in future versions, but v0.1 tools MUST at least understand and interoperate with these seven tags.

### 5.2 Tagging Unit

A **Tagging Unit** is the minimal element to which a single Functional Tag is applied.

Normative requirements:

- A Tagging Unit MUST at least contain:
  - A reference to the underlying SD unit (e.g., `mp_id`), and  
  - A single **primary** Functional Tag from the v0.1 set.
- A Tagging Unit MAY include:
  - one or more **secondary** tags (e.g., FRAME+TENSION),
  - a **confidence score** for the primary tag,
  - local Tension snapshot or other metadata.

The granularity (utterance-level, clause-level, turn-level) is implementation-specific,  
but MUST be documented by the implementation.

### 5.3 Tagging Output Format (Informative)

A typical tagged session MAY look like:

```yaml
session_id: "sess-2025-11-27-001"
subject_id: "anon-123"

tagged_units:
  - unit_id: "u1"
    mp_id: "mp-01"
    primary_tag: "FRAME"
    secondary_tags: []
    confidence: 0.87
    tension:
      types: ["vagueness"]
      intensity: 0.6

  - unit_id: "u2"
    mp_id: "mp-02"
    primary_tag: "ABSTRACT"
    secondary_tags: []
    confidence: 0.81

  - unit_id: "u3"
    mp_id: "mp-03"
    primary_tag: "STRUCTURE"
    secondary_tags: []
    confidence: 0.78

  - unit_id: "u4"
    mp_id: "mp-04"
    primary_tag: "TENSION"
    secondary_tags: []
    confidence: 0.90
    tension:
      types: ["boundary_conflict"]
      intensity: 0.8

  - unit_id: "u5"
    mp_id: "mp-05"
    primary_tag: "DECIDE"
    secondary_tags: []
    confidence: 0.72
```

### 5.4 Tagger Conformance

A Tagging component is **conformant** if:
1. It produces only valid v0.1 Functional Tags as primary tags.
2. It outputs a well-formed sequence of Tagging Units for each processed SD session.
3. It documents:
  - Tagging granularity (unit definition),
  - Tagging method (rule-based, model-based, hybrid),
  - Any usage of Tension or SD phase labels.
The spec does **not** mandate any particular tagging algorithm.

---

## 6. Cognitive Rhythm Descriptor (CR-Descriptor v0.1)

### 6.1 Purpose

A **CR-Descriptor** summarizes:
- How often different Functional Tags appear.
- How they tend to follow each other.
- How Tension and other drivers influence transitions.
- High-level stability patterns (e.g., loops, decision style).

It is **descriptive**, not executable.


### 6.2 Minimal Fields

A CR-Descriptor v0.1 MUST contain:
- `subject_id` – identifier of the subject.
- `data_scope` – description of the data window used (e.g., "last 40 sessions", date range).
- `functional_distribution` – normalized frequencies of tags.
- `transitions` – aggregated transition statistics between tags.

It MAY additionally contain:
- `driver_correlations` – mapping from Tension types/intensities to transition patterns.
- `stability` – information about loops, volatility, and overload-prone phases.
- Implementation-specific metadata (version, model, notes).

6.3 Example CR-Descriptor (YAML)

```
cr_descriptor:
  subject_id: "anon-123"
  data_scope:
    type: "session_count"
    value: 40

  functional_distribution:
    OBSERVE:   0.08
    FRAME:     0.22
    ABSTRACT:  0.18
    STRUCTURE: 0.20
    TENSION:   0.22
    DECIDE:    0.07
    META:      0.03

  transitions:
    "FRAME->ABSTRACT":
      prob: 0.63
      count: 124
      typical_tension: ["vagueness", "ambiguity"]

    "ABSTRACT->STRUCTURE":
      prob: 0.54
      count: 91
      typical_tension: ["need_for_action"]

    "STRUCTURE->TENSION":
      prob: 0.71
      count: 137
      typical_tension: ["boundary_conflict", "overload"]

    "TENSION->FRAME":
      prob: 0.29
      count: 52
      typical_tension: ["misframing"]

    "TENSION->DECIDE":
      prob: 0.18
      count: 34
      typical_tension: ["low_residual_tension", "deadline"]

  driver_correlations:
    by_tension_type:
      misframing:
        boosts_transitions: ["TENSION->FRAME"]
      boundary_conflict:
        boosts_transitions: ["TENSION->STRUCTURE"]
      overload:
        boosts_transitions: ["TENSION->OBSERVE", "TENSION->META"]

  stability:
    dominant_loops:
      - ["FRAME", "ABSTRACT", "STRUCTURE", "TENSION"]
    decision_style: "late_commit_with_rework"
    overload_prone_phases: ["Divergence", "Resonance"]

  meta:
    generated_at: "2025-11-27T12:00:00Z"
    engine_version: "de-v0.1.0"
```

### 6.4 Aggregation Requirements

A CR-Descriptor Generator is conformant if it:

1. Aggregates statistics over a **clearly specified data_scope**.
2. Computes functional_distribution as a **normalized** (sum ≈ 1.0) distribution.
3. Computes transition probabilities over adjacent tagged units:
  - The exact adjacency definition (within-session, cross-session) MUST be documented.
4. If Tension information is available:
  - Computes driver_correlations in a reproducible way (heuristics MUST be documented).

The spec does not require a specific statistical method (frequentist, Bayesian, etc.),
only that the resulting structure is well-formed and documented.

---

## 7. RhythmSpec Synthesis

### 7.1 Purpose
The **Rhythm Compiler** transforms a CR-Descriptor into **candidate RhythmSpecs** that:
- Are executable by SEA Runtimes (§6.2 of SEA Core), and
- Approximate the observed cognitive rhythm of the subject.

--- 

### 7.2 Inputs and Outputs

- **Input**: One or more CR-Descriptors for a subject (optionally segmented by domain/context).
- **Output**:
  - One or more RhythmSpec documents, each with:
    - `id`, `version`, `description`,
    - `states`, `initial_state`, `transitions`, `termination` (SEA-compatible).

--- 

### 7.3 Synthesis Steps (Non-normative Pattern)

A typical synthesis pipeline MAY:

1. Select States
- Choose a subset of Functional Tags as explicit states.
- Common v0.1 choice:
  - FRAME, ABSTRACT, STRUCTURE, TENSION, DECIDE,
  - optionally OBSERVE and META if they cross a usage threshold.

2. Derive Transitions

- For each tag pair A→B with probability above a threshold (e.g., prob >= 0.05), construct a candidate transition.
- Add metadata from driver_correlations as conditions:
  - e.g., tension type misframing → TENSION→FRAME.

3. Set Initial State and Termination

- Initial state:
  - often the most likely entry tag (e.g., FRAME),
  - or specified by design (e.g., OBSERVE or FRAME).

- Termination conditions:
  - low residual Tension + DECIDE + explicit stop signal,
  - or other domain-specific criteria.

4. Simplify and Regularize
- Optionally:
  - merge low-frequency transitions into a fallback pattern,
  - cap the number of states for interpretability,
  - add meta-rules (e.g., loops limited to N iterations).

---

### 7.4 Example Synthesis Result

See **SEA Appendix B – "Simen Default Rhythm"** as a fully worked example.
The Discovery Engine for Simen would have:

- Observed a high-frequency loop: FRAME → ABSTRACT → STRUCTURE → TENSION,
- Observed Tension-type driven transitions back:
  - misframing → TENSION→FRAME,
  - pattern_gap → TENSION→ABSTRACT,
  - boundary_conflict → TENSION→STRUCTURE,
- Encoded these into a RhythmSpec.

--- 

### 7.5 Synthesis Conformance

A Rhythm Compiler is conformant if:
1. It produces RhythmSpecs that satisfy SEA Core RhythmSpec requirements.
2. It documents:
  - which Functional Tags are mapped to which states,
  - which thresholds and heuristics are used to select transitions,
  - how Tension types are mapped to transition conditions.

3. It keeps a traceable link between CR-Descriptors and generated RhythmSpecs (e.g., via IDs, versioning, or provenance metadata).

The spec does not require the synthesized rhythm to be optimal—only well-formed and explainable.

---

## 8. Profile Hint Generation

### 8.1 Purpose

**Profile Hints** summarize qualitative tendencies inferred from CR-Descriptors, to be used as inputs for SEA Profiles (bias parameters).

Examples:
- "frame-heavy, late decision",
- "observe-heavy under overload",
- "structure-centric under high tension".

---

### 8.2 Example Structure

```yaml
profile_hints:
  subject_id: "anon-123"
  derived_from_cr_descriptor: "crd-2025-11-27-001"

  tendencies:
    framing_style: "intent-first"
    decision_style: "late_commit_with_rework"
    structure_dependency: "high"
    meta_reflection_usage: "low"

  tension_behaviors:
    under_misframing:
      preferred_shift: "FRAME"
    under_boundary_conflict:
      preferred_shift: "STRUCTURE"
    under_overload:
      preferred_shift: "OBSERVE"

  notes:
    - "Subject tends to re-open framing when TENSION remains high after STRUCTURE."
    - "DECIDE rarely appears without at least one TENSION cycle."
```

---

### 8.3 Non-Normative Status

- Profile hints are optional and **non-normative** in v0.1.
- Different systems MAY:
  - ignore them,
  - use them to initialize SEA Profiles,
  - or combine them with explicit human self-report.

---

## 9. Conformance Classes

This spec defines three **conformance classes** for Discovery Engines:

### 9.1 Tagging-Only Engine

A Tagging-Only Discovery Engine:
- MUST implement Functional Tagging as per §5.
- MUST output tagged sequences for SD Logs.
- MUST NOT claim conformance to CR-Descriptor or RhythmSpec synthesis classes.

### 9.2 Descriptor-Generating Engine

A Descriptor-Generating Discovery Engine:
- MUST satisfy Tagging-Only requirements.
- MUST generate CR-Descriptors as per §6.
- MUST document data_scope and aggregation choices.

### 9.3 Rhythm-Synthesizing Engine

A Rhythm-Synthesizing Discovery Engine:
- MUST satisfy Descriptor-Generating requirements.
- MUST implement RhythmSpec synthesis as per §7.
- MAY implement Profile Hint generation as per §8.
- MUST produce SEA-compatible RhythmSpecs.

Implementers SHOULD clearly advertise which conformance class they support.

---

## 10. Privacy and Safety (Non-Normative Considerations)

Because Discovery Engines operate on personal reasoning logs, implementers SHOULD:

- Treat SD Logs and CR-Descriptors as **sensitive data**.
- Offer mechanisms for:
  - subject opt-in / opt-out,
  - data deletion and CR-Descriptor revocation,
  - anonymization where appropriate.
- Avoid using CR-Descriptors to make irreversible high-stakes decisions
(e.g., hiring / firing) without additional safeguards and human oversight.

---

# Appendix A – Summary of Core Data Structures (Informative)

## A.1 Tagging Unit (JSON Schema Sketch)

```json
{
  "type": "object",
  "required": ["unit_id", "mp_id", "primary_tag"],
  "properties": {
    "unit_id": { "type": "string" },
    "mp_id": { "type": "string" },
    "primary_tag": {
      "type": "string",
      "enum": ["OBSERVE", "FRAME", "ABSTRACT", "STRUCTURE", "TENSION", "DECIDE", "META"]
    },
    "secondary_tags": {
      "type": "array",
      "items": {
        "type": "string",
        "enum": ["OBSERVE", "FRAME", "ABSTRACT", "STRUCTURE", "TENSION", "DECIDE", "META"]
      }
    },
    "confidence": { "type": "number", "minimum": 0.0, "maximum": 1.0 },
    "tension": {
      "type": "object",
      "properties": {
        "types": { "type": "array", "items": { "type": "string" } },
        "intensity": { "type": "number" }
      }
    }
  }
}
```

## A.2 CR-Descriptor (JSON Schema Sketch)

```json
{
  "type": "object",
  "required": ["subject_id", "data_scope", "functional_distribution", "transitions"],
  "properties": {
    "subject_id": { "type": "string" },
    "data_scope": {
      "type": "object",
      "required": ["type", "value"],
      "properties": {
        "type": { "type": "string" },
        "value": {}
      }
    },
    "functional_distribution": {
      "type": "object",
      "properties": {
        "OBSERVE":  { "type": "number" },
        "FRAME":    { "type": "number" },
        "ABSTRACT": { "type": "number" },
        "STRUCTURE":{ "type": "number" },
        "TENSION":  { "type": "number" },
        "DECIDE":   { "type": "number" },
        "META":     { "type": "number" }
      },
      "additionalProperties": false
    },
    "transitions": {
      "type": "object",
      "additionalProperties": {
        "type": "object",
        "required": ["prob"],
        "properties": {
          "prob": { "type": "number", "minimum": 0.0, "maximum": 1.0 },
          "count": { "type": "integer", "minimum": 0 },
          "typical_tension": {
            "type": "array",
            "items": { "type": "string" }
          }
        }
      }
    },
    "driver_correlations": { "type": "object" },
    "stability": { "type": "object" },
    "meta": { "type": "object" }
  }
}
```

