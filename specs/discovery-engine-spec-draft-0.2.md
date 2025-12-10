# Discovery Engine Specification – Draft 0.2

> **Document ID:** discovery-engine-spec-draft-0.2  
> **Status:** Draft (v0.2, non-final)  
> **Intended Audience:** System architects, researchers, and implementers of Semantic-Dynamics-compatible systems.

---

## 0. Purpose and Scope

This document specifies the **Discovery Engine**.

The Discovery Engine:

- Observes **Semantic Dynamics (SD)**–style flows from reflection or reasoning processes,
- Infers a subject’s **Cognitive Rhythm** over time,
- Emits **descriptive artifacts** (Cognitive Rhythm Descriptors) that summarize how semantic movement tends to unfold.

This specification defines:

- The expected **input data model** (SD Logs),
- The **Tagging model** (Functional Tags and Tagging Units),
- The format and semantics of **Cognitive Rhythm Descriptors (CR-Descriptors)**,
- Conformance requirements for implementations.

The document is **implementation-agnostic**:

- It does **not** prescribe any particular learning algorithm, model, or runtime.
- It does **not** depend on any specific architecture or framework.
- It assumes only that input data can be expressed in the vocabulary of **Semantic Dynamics**.

Normative requirements are explicitly marked using **MUST**, **SHOULD**, and **MAY** (as in RFC 2119).  
Other content is informative/non-normative.

---

## 1. Relationship to Semantic Dynamics

**Semantic Dynamics (SD)** provides a descriptive theory of how meaning moves in human thinking and self-reflection. It defines, among other things:

- **Mind-pops** – small, tension-laden units of semantic emission,
- **Threads and branches** – meso-level structures that form around enduring Tensions,
- **Dynamics phases** – typical stages such as Emergence, Divergence, Resonance, Distillation, Stabilization, Crystallization, Rest,
- **Regimes / failure modes** – patterns such as Runaway Divergence, Premature Stabilization, Chronic Oscillation, Semantic Overload, Collapsed Flow, Misaligned Phase-lock,
- **Recovery patterns** – how systems move from less healthy regimes back to healthier flow,
- **Cognitive rhythm descriptors** – summaries of how a subject tends to move through these phases and regimes over time.

The Discovery Engine:

- **Consumes** SD-structured logs (sessions, mind-pops, optional phases and Tensions),
- **Reconstructs** semantic flow over time,
- **Aggregates** these flows into **Cognitive Rhythm Descriptors** that make the subject’s typical dynamics explicit.

The Discovery Engine **does not modify** the semantics of Semantic Dynamics.  
Instead, it uses SD as its **coordinate system** for observation and summarization.

---

## 2. Terminology

The following terms are used throughout this specification.

| Term | Definition |
|------|------------|
| **Subject** | The entity whose semantic dynamics are being analyzed (e.g., an individual human, a team, or an artificial agent). |
| **SD Log** | A structured record of one or more reflection / reasoning sessions, expressed in the vocabulary of Semantic Dynamics (mind-pops, Tensions, optional phases and regimes). |
| **Session** | A coherent reflection or reasoning episode for a subject. |
| **Mind-pop** | A minimal SD unit of semantic emission: an utterance, note, or internal thought that carries tension-laden meaning. |
| **Thread** | A time-ordered sequence of mind-pops revisiting a shared Tension or topic. |
| **Branch** | A set of threads that explore distinct semantic trajectories around the same underlying Tension. |
| **Tension Snapshot** | A local description of Tension types and intensities at a point in the semantic flow. |
| **Dynamics Phase** | One of the SD phases (Emergence, Divergence, Resonance, Distillation, Stabilization, Crystallization, Rest). |
| **Regime** | A higher-level SD pattern such as Runaway Divergence, Premature Stabilization, Chronic Oscillation, Semantic Overload, Collapsed Flow, or Misaligned Phase-lock. |
| **Tagging Unit** | The minimal element to which a Functional Tag is applied by the Discovery Engine. |
| **Functional Tag** | A label describing the functional role of a Tagging Unit in the ongoing semantic process (e.g., OBSERVE, FRAME, ABSTRACT). |
| **Cognitive Rhythm Descriptor (CR-Descriptor)** | A descriptive summary of a subject’s long-term semantic dynamics, aggregating statistics over SD phases, regimes, Tensions, and (optionally) Functional Tags. |
| **Data Scope** | The window of SD Logs used to generate a given CR-Descriptor (e.g., "last N sessions" or a date range). |

Where appropriate, **MUST**, **SHOULD**, and **MAY** are interpreted as in RFC 2119.

---

## 3. Architectural Overview and Responsibilities

This section describes the logical responsibilities of a Discovery Engine. It does not constrain internal architecture.

A conformant Discovery Engine has three top-level responsibilities:

1. **Flow Reconstruction**
2. **Tagging**
3. **Aggregation into CR-Descriptors**

A concrete implementation MAY merge or split these responsibilities internally,  
but a conformant Discovery Engine **MUST** make all three responsibilities identifiable in its behavior and artifacts.

### 3.1 Flow Reconstruction

Given SD Logs, the engine:

- Orders mind-pops in time,
- Associates them with sessions (and, when available, threads and branches),
- Uses any explicit SD annotations (phases, regimes, Tension snapshots),
- Optionally infers phases or regimes when they are not explicitly labeled.

Normative requirements:

- The engine **MUST** preserve the original temporal ordering of mind-pops within each session.
- When existing thread or branch identifiers are present, the engine **MUST NOT** silently discard them.
- If the engine infers phases or regimes, it **MUST** record that these labels are inferred and **MUST** expose this distinction in downstream artifacts (e.g., CR-Descriptor metadata).

### 3.2 Tagging

The engine:

- Segments the semantic flow into **Tagging Units**,
- Assigns **Functional Tags** to these units to describe their functional role,
- Records optional metadata such as Tension snapshots and confidence scores.

Normative requirements:

- Each Tagging Unit **MUST** reference at least one underlying SD record (e.g., a mind-pop).
- A Tagging Unit **MUST** have exactly one **primary** Functional Tag.
- A Tagging Unit **MAY** have zero or more **secondary** Functional Tags.
- The engine **SHOULD** record a confidence score for the primary tag when such a measure is meaningful.

### 3.3 Aggregation into CR-Descriptors

Over a specified **Data Scope**, the engine:

- Aggregates statistics about:
  - SD phases and transitions,
  - regimes and recovery patterns,
  - Tension types and intensities,
  - usage patterns of Functional Tags,
- Produces one or more **CR-Descriptors** that summarize these patterns for the subject.

Normative requirements:

- The engine **MUST** record the Data Scope used for each CR-Descriptor.
- If Functional Tagging is available, the engine **SHOULD** include tag-based distributions and transitions.
- If any SD dimension (e.g., regimes, Tension types) is missing or sparse, the engine **SHOULD** either:
  - omit the corresponding fields, or
  - mark them as low-confidence.

---

## 4. Input Data Model – SD Logs

This section describes the minimum structure of SD Logs required by a Discovery Engine.

A Discovery Engine **MUST** document:

- The exact fields it expects,
- How it treats missing or partial information (e.g., absent phase labels, missing tension annotations).

The structures below are indicative; concrete encodings (JSON, YAML, database schemas) are implementation-specific.

### 4.1 Session

A **Session** is a coherent reflection / reasoning episode.

Typical fields:

- `session_id` – unique identifier.
- `subject_id` – identifier of the subject.
- `timestamp_start`, `timestamp_end` – temporal bounds.
- `context` – free-form or structured description (domain, task, stakes).
- `mind_pops` – ordered list of mind-pop entries.
- Optional: `phases` – session-level SD phase summary.
- Optional: `global_tension_summary` – high-level description of Tensions present in the session.

Normative requirement:

- A Discovery Engine **MUST** be able to map each Tagging Unit back to a `session_id`.

### 4.2 Mind-Pop

A **Mind-Pop** is a minimal SD unit of semantic emission.

Typical fields:

- `mp_id` – unique identifier (within or across sessions).
- `role` – e.g., `human`, `engine`, `observer`, or similar.
- `text` – raw text content.
- `timestamp` – temporal marker (relative or absolute).
- Optional: `thread_id` – association with a Thread.
- Optional: `branch_id` – association with a Branch.
- Optional: `sd_phase_label` – e.g., Emergence, Divergence, Resonance, Distillation, Stabilization, Crystallization, Rest.
- Optional: `regime_label` – e.g., RunawayDivergence, PrematureStabilization, ChronicOscillation, SemanticOverload, CollapsedFlow, MisalignedPhaseLock.

Normative requirements:

- A Discovery Engine **MUST** preserve the mapping `TaggingUnit → mp_id`.
- If `sd_phase_label` or `regime_label` are absent, the engine **MAY** infer them but **MUST** record that they are inferred.

### 4.3 Tension Snapshot

A **Tension Snapshot** describes local Tension around one or more mind-pops.

Typical fields:

- `tension_types` – implementation-specific labels grounded in SD’s notion of Tension  
  (e.g., `"stability_vs_exploration"`, `"should_vs_feel"`, `"overload"`).
- `tension_intensity` – numeric or ordinal measure.
- `tension_notes` – free-text description.

Tension Snapshots MAY be attached:

- directly to mind-pops,
- to Tagging Units,
- or to larger segments (e.g., spans of the session).

Normative requirement:

- A Discovery Engine **MUST** be able to operate in a "Tension-light" mode where Tension labels are sparse or absent.  
  In this case, any Tension-based statistics in CR-Descriptors **SHOULD** be marked as low-confidence or omitted.

### 4.4 Partial and Derived Annotations

Implementations MAY:

- Accept SD Logs that already contain:
  - `sd_phase_label` per mind-pop or segment,
  - `regime_label` per segment or episode,
- Or derive such labels using internal heuristics or models.

In either case, the Discovery Engine **MUST**:

- Document whether phases/regimes are **observed** or **inferred**,
- Record this origin (observed vs inferred) in CR-Descriptor metadata when relevant.

---

## 5. Functional Tagging

This section defines the Functional Tagging model used by the Discovery Engine.

Functional Tags are **not part of Semantic Dynamics itself**.  
They are an additional analytic axis introduced by this specification to characterize:

- what each segment of the semantic flow is *doing* functionally  
  (e.g., observing, framing, abstracting, structuring, focusing on tension, deciding, meta-reflecting).

### 5.1 Functional Tag Set v0.2

The Discovery Engine **MUST** support at least the following Functional Tags:

- `OBSERVE`  
  - Gathering or revisiting inputs, reading, retrieving, invoking tools or data.

- `FRAME`  
  - Naming, reframing, clarifying intent and scope, posing or refining questions.

- `ABSTRACT`  
  - Extracting patterns, invariants, conceptual schemas, or general principles.

- `STRUCTURE`  
  - Decomposing into parts, layers, responsibilities, interfaces; arranging into structures.

- `TENSION`  
  - Surfacing conflicts, trade-offs, fog, overload, misalignment; zooming in on Tension.

- `DECIDE`  
  - Committing to a direction, structural choice, decision, or next step.

- `META`  
  - Reflecting on the process or rhythm itself; adjusting approach, granularity, or focus.

Additional tags MAY be defined in future versions or vendor extensions.  
However, v0.2 tools and artifacts **MUST** at least understand and interoperate with these seven tags.

### 5.2 Tagging Unit

A **Tagging Unit** is the minimal element to which a single primary Functional Tag is applied.

Normative requirements:

- A Tagging Unit **MUST** at least contain:
  - A reference to the underlying SD unit (e.g., `mp_id`), and
  - A single **primary** Functional Tag from the v0.2 set.
- A Tagging Unit **MAY** include:
  - one or more **secondary** tags (e.g., `FRAME`+`TENSION`),
  - a **confidence score** for the primary tag,
  - a local Tension Snapshot or other metadata,
  - references to `thread_id`, `branch_id`, or `session_id`.

The granularity of Tagging Units (utterance-level, clause-level, turn-level, or segment-level) is implementation-specific,  
but **MUST** be documented by the implementation.

### 5.3 Tagging Output Format (Informative)

The following YAML snippet illustrates a possible tagging output format:

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
    secondary_tags: ["TENSION"]
    confidence: 0.79
    tension:
      types: ["boundary_conflict"]
      intensity: 0.7
```

Implementations MAY choose different concrete encodings (e.g., JSON, Protobuf),
as long as the semantics of Tagging Units and Functional Tags are preserved.

---

## 6. Cognitive Rhythm Descriptor (CR-Descriptor v0.2)

### 6.1 Purpose

A **Cognitive Rhythm** Descriptor (CR-Descriptor) is a descriptive summary of:
- How the subject tends to occupy and transition between SD **phases**,
- How often and under what conditions various **regimes** appear,
- How **Tension** types and intensities relate to these dynamics,
- How **Functional Tags** are distributed and transition over time (optional but recommended).

It is **descriptive**, not executable.

External systems MAY consume CR-Descriptors to adapt their behavior,
but such usages are outside the scope of this specification.


### 6.2 Minimal Fields

A CR-Descriptor v0.2 MUST contain:
- `subject_id` – identifier of the subject.
- `data_scope` – description of the SD Log window used (e.g., "last 40 sessions", date range).
- `phase_distribution` – normalized frequencies over SD phases
(Emergence, Divergence, Resonance, Distillation, Stabilization, Crystallization, Rest).
- `phase_transitions` – aggregated transition statistics between SD phases.
- `regime_statistics` – frequencies or rates of SD regimes (where observed or inferred).
- `tension_profile` – summary of Tension types and intensities encountered.

A CR-Descriptor v0.2 **SHOULD** also contain, when Functional Tagging is available:
- `functional_distribution` – normalized frequencies of Functional Tags.
- `functional_transitions` – aggregated transition statistics between Functional Tags.

It **MAY** additionally contain:
- `thread_metrics` – statistics about threads (e.g., typical length, survival rate).
- `branch_metrics` – statistics about branches (e.g., average number of active branches, fragmentation indices).
- `recovery_patterns` – common transitions from failure regimes back to healthier flow.
- `natural_language_summary` – human-readable description of the subject’s rhythm.
- `implementation_metadata` – version, model, notes, confidence levels for derived quantities.


### 6.3 Aggregation Requirements

A CR-Descriptor generator is conformant if it:
1. Aggregates statistics over a **clearly specified data_scope**.
2. Computes `phase_distribution` as a normalized distribution (sum ≈ 1.0, within numerical tolerance).
3. Defines adjacency for `phase_transitions` (within-session, cross-session) and documents it.
4. Uses any available Tension and regime labels in a documented, reproducible way.
5. Clearly documents any heuristics used to infer phases, regimes, or Tension types when explicit labels are absent.
6. If Functional Tagging is used:
  - Computes `functional_distribution` and `functional_transitions` using the Tagging Units,
  - Documents the granularity and segmentation strategy used for tagging.


### 6.4 Example (Informative)

```yaml
cr_descriptor:
  subject_id: "anon-123"
  data_scope:
    type: "session_count"
    value: 40

  phase_distribution:
    Emergence:       0.18
    Divergence:      0.24
    Resonance:       0.20
    Distillation:    0.16
    Stabilization:   0.10
    Crystallization: 0.07
    Rest:            0.05

  regime_statistics:
    RunawayDivergence:
      freq: 0.12
    PrematureStabilization:
      freq: 0.05
    ChronicOscillation:
      freq: 0.08
    SemanticOverload:
      freq: 0.10
    CollapsedFlow:
      freq: 0.03

  tension_profile:
    common_types: ["stability_vs_exploration", "should_vs_feel", "overload"]
    avg_intensity: 0.64

  phase_transitions:
    "Emergence->Divergence":
      prob: 0.61
    "Divergence->Distillation":
      prob: 0.42
    "Distillation->Stabilization":
      prob: 0.38
    "Stabilization->Rest":
      prob: 0.55

  functional_distribution:
    OBSERVE:   0.08
    FRAME:     0.22
    ABSTRACT:  0.18
    STRUCTURE: 0.20
    TENSION:   0.17
    DECIDE:    0.10
    META:      0.05

  implementation_metadata:
    generator_version: "de-0.2.0"
    notes: "Phase labels inferred for 60% of mind-pops."
```

The exact internal estimation methods are implementation-specific,
but the semantics of the fields **MUST** match the descriptions above.

---

## 7. Operational Considerations (Informative)

This section is non-normative and provides guidance for implementers.

### 7.1 Tagging Strategies and Granularity

- **Utterance-level units** are often simpler to implement, mapping one mind-pop to one Tagging Unit.
- **Segment-level units** can better capture higher-level moves (e.g., a multi-sentence reframing).
- Implementers **SHOULD**:
  - Choose a consistent segmentation strategy,
  - Document the strategy in implementation metadata,
  - Consider including both fine-grained and coarse-grained views if useful.


### 7.2 Handling Partial and Noisy Data

Real-world SD Logs may be:
- Partially annotated (missing phase/regime labels),
- Noisy (ambiguous or conflicting labels),
- Sparse in Tension information.

Implementations are encouraged to:
- Use confidence scores for inferred labels,
- Provide "degraded" CR-Descriptors that omit fields when data is insufficient,
- Track data quality and expose it in `implementation_metadata`.

---

### 7.3 Incremental vs Batch Operation

Discovery Engines may operate:
- In **batch mode**, aggregating large windows of past SD Logs,
- In **incremental / streaming** mode, updating CR-Descriptors as new sessions arrive.

Implementers **SHOULD**:
- Specify whether CR-Descriptors are computed from scratch or incrementally updated,
- Consider including a `revision` or `generation_timestamp` in `implementation_metadata`.


### 7.4 Multi-Subject Data

When multiple subjects are present:
- CR-Descriptors **SHOULD** be produced per subject.
- Group-level descriptors (e.g., for a team) **MAY** be defined as additional artifacts,
but these are outside the core v0.2 scope.

---

## 8. Extensibility

The Discovery Engine specification is designed to be extensible while preserving interoperability.

### 8.1 Tag Set Evolution

Future versions or vendor extensions MAY:
- Introduce new Functional Tags,
- Refine definitions of existing tags.

However:
- The original seven tags (OBSERVE, FRAME, ABSTRACT, STRUCTURE, TENSION, DECIDE, META) **MUST** remain available and interoperable in v0.x series.
- Implementations introducing new tags **SHOULD**:
  - Document their semantics,
  - Provide a mapping back to the v0.2 base set where possible.


### 8.2 Custom Fields in CR-Descriptors

Implementations MAY add custom fields to CR-Descriptors, for example:
- Additional distributions (e.g., domain-specific states),
- Derived metrics (e.g., "exploration vs exploitation index"),
- Clustering information (e.g., typical "session archetypes").

Normative requirements:
- Custom fields **MUST NOT** override or change the semantics of mandatory fields.
- Custom fields **SHOULD** be namespaced or clearly documented to avoid collisions.


### 8.3 Vendor-Specific Extensions

Vendors MAY publish:
- Profiles that constrain optional parts of this spec,
- Additional conformance levels,
- Mappings from CR-Descriptors to other systems.

Such extensions:
- **MUST NOT** claim conformance to this specification if they violate normative requirements.
- **SHOULD** clearly separate normative references to this spec from their proprietary additions.

---

## 9. Conformance

This section defines what it means to conform to the Discovery Engine Specification v0.2.

### 9.1 Implementation Classes

Two main classes of conformant implementations are defined:
1. Descriptor-Generating Discovery Engine
2. Descriptor-Inspecting Tool

#### 9.1.1 Descriptor-Generating Discovery Engine

A Descriptor-Generating Discovery Engine:
- Consumes SD Logs as described in §4,
- Performs Flow Reconstruction (§3.1),
- Performs Functional Tagging (§3.2, §5),
- Aggregates results into CR-Descriptors (§3.3, §6).

To be conformant, it **MUST**:
- Support the minimal SD Log structures described in §4,
- Implement the Functional Tag set v0.2 (§5.1),
- Produce CR-Descriptors containing at least the fields listed in §6.2,
- Respect the aggregation requirements listed in §6.3.

#### 9.1.2 Descriptor-Inspecting Tool

A Descriptor-Inspecting Tool:
- Consumes CR-Descriptors v0.2,
- Provides visualization, analysis, or reporting,
- Does not necessarily have access to raw SD Logs.

To be conformant, it **MUST**:
- Correctly interpret the semantics of mandatory CR-Descriptor fields (§6.2),
- Not rely on any custom field being present,
- Handle missing optional fields gracefully.


### 9.2 Minimal Conformance

The minimal conformance profile is a Descriptor-Generating Discovery Engine that:
- Accepts SD Logs with:
  - Sessions,
  - Mind-pops (with timestamps and text),
  - Optional or no Tension and phase labels,
- Assigns Functional Tags at the mind-pop level,
- Produces CR-Descriptors with:
  - `subject_id`,
  - `data_scope`,
  - `phase_distribution` (with inferred phases if necessary),
  - `phase_transitions`,
  - `functional_distribution`,
  - `functional_transitions`.

Regime-related fields and detailed Tension profiles are **RECOMMENDED** but not strictly required for minimal conformance.


### 9.3 Testing Guidelines (Informative)

Conformance testing may include:
- Synthetic SD Logs where phases and regimes are controlled and known,
- Known tag patterns (e.g., sequences of OBSERVE → FRAME → ABSTRACT),
- Cross-engine comparison of CR-Descriptors on the same SD Log.

These guidelines are informative and do not constitute a formal test suite.

---

## 10. Security and Privacy Considerations

Discovery Engines often operate on highly sensitive reflection data.
Although this specification is primarily structural and semantic, implementations **SHOULD** consider the following:


### 10.1 Data Sensitivity

- SD Logs may contain:
  - Personal identifiers,
  - Emotional states,
  - Internal conflicts and Tensions.

Recommendations:
- Implement strong access control for SD Logs and CR-Descriptors.
- Treat both SD Logs and CR-Descriptors as potentially sensitive.
- Consider pseudonymization or anonymization of `subject_id` where possible.

### 10.2 Re-Identification Risk

- Even aggregated descriptors can inadvertently reveal patterns that identify an individual.
- When sharing or publishing CR-Descriptors, implementers **SHOULD**:
  - Strip or hash `subject_id`,
  - Aggregate over multiple subjects when appropriate,
  - Avoid including verbatim text or rare combinations of fields.


### 10.3 Integrity and Tampering

* Modifications to SD Logs or CR-Descriptors can lead to incorrect inferences about a subject’s cognitive rhythm.
* Implementers **SHOULD**:
  - Consider cryptographic integrity mechanisms when required,
  - Log provenance and generation steps in `implementation_metadata`.


### 10.4 Consent and Transparency

- Subjects whose data are analyzed by a Discovery Engine **SHOULD**:**
  - Be informed about the existence and purpose of the engine,
  - Be able to request deletion of their data where applicable,
  - Receive human-readable explanations of what CR-Descriptors represent.

These considerations are non-normative but strongly recommended.

---

## 11. Versioning and Migration

### 11.1 Version Identifier

Implementations **SHOULD** include a version identifier in:
- CR-Descriptors (e.g., `cr_descriptor_version: "0.2"`),
- Tagging outputs (e.g., `tag_schema_version: "0.2"`),
- Implementation metadata.


### 11.2 Backward Compatibility

When moving from one minor version to another (e.g., 0.2 → 0.3):
- Existing mandatory fields **SHOULD** remain with unchanged semantics.
- New fields **SHOULD** be optional.
- Deprecated fields **SHOULD** be marked as such for at least one minor version before removal.


### 11.3 Migration Strategies

Implementers MAY provide:
- Migration utilities to transform older CR-Descriptors into the newer format,
- Compatibility layers that accept multiple CR-Descriptor versions.

Such utilities are outside the scope of this specification but are recommended for practical deployments.

---

## 12. Glossary (Informative)

- **Semantic Dynamics (SD)** – A theoretical framework describing how meaning moves through phases, regimes, and Tensions in reflective or reasoning processes.
- **Discovery Engine** – A component that consumes SD Logs, tags semantic flow, and produces Cognitive Rhythm Descriptors.
- **Cognitive Rhythm** – The characteristic way a subject tends to move through SD phases, regimes, and Tensions over time.
- **Cognitive Rhythm Descriptor (CR-Descriptor)** – A structured artifact summarizing a subject’s Cognitive Rhythm.
- **Functional Tag** – A label capturing the functional role of a segment of semantic flow (e.g., observing, framing, abstracting).
- **Tagging Unit** – The minimal element to which a Functional Tag is applied.
- **Regime** – A recurring dynamics pattern or failure mode in SD, such as Runaway Divergence or Premature Stabilization.
- **Tension** – The driving force behind semantic movement in SD, encompassing conflict, uncertainty, overload, fog, and other forms of felt pressure.

---

## Appendix A. Example SD Log and Discovery Process (Informative)

This appendix provides a non-normative worked example.


### A.1 Example SD Log (Simplified)

```yaml
session_id: "sess-001"
subject_id: "anon-123"
timestamp_start: "2025-11-27T09:00:00Z"
timestamp_end: "2025-11-27T09:30:00Z"

mind_pops:
  - mp_id: "mp-01"
    role: "human"
    text: "I feel overloaded by too many projects."
    timestamp: "2025-11-27T09:01:00Z"
    sd_phase_label: "Emergence"

  - mp_id: "mp-02"
    role: "human"
    text: "Maybe I should list them all and see which ones truly matter."
    timestamp: "2025-11-27T09:02:30Z"
    sd_phase_label: "Divergence"

  - mp_id: "mp-03"
    role: "human"
    text: "Actually, there are three main streams: teaching, management, and new products."
    timestamp: "2025-11-27T09:05:00Z"
    sd_phase_label: "Resonance"

tension_snapshots:
  - at_mp_id: "mp-01"
    tension_types: ["overload"]
    tension_intensity: 0.8

  - at_mp_id: "mp-03"
    tension_types: ["stability_vs_exploration"]
    tension_intensity: 0.6
```


### A.2 Example Tagging Output

```yaml
session_id: "sess-001"
subject_id: "anon-123"
tag_schema_version: "0.2"

tagged_units:
  - unit_id: "u1"
    mp_id: "mp-01"
    primary_tag: "TENSION"
    secondary_tags: ["FRAME"]
    confidence: 0.92
    tension:
      types: ["overload"]
      intensity: 0.8

  - unit_id: "u2"
    mp_id: "mp-02"
    primary_tag: "FRAME"
    secondary_tags: ["STRUCTURE"]
    confidence: 0.88

  - unit_id: "u3"
    mp_id: "mp-03"
    primary_tag: "ABSTRACT"
    secondary_tags: []
    confidence: 0.85
    tension:
      types: ["stability_vs_exploration"]
      intensity: 0.6
```


### A.3 Example CR-Descriptor Derived from Multiple Sessions

```yaml
cr_descriptor:
  subject_id: "anon-123"
  cr_descriptor_version: "0.2"
  data_scope:
    type: "date_range"
    from: "2025-10-01"
    to: "2025-11-30"

  phase_distribution:
    Emergence:       0.15
    Divergence:      0.27
    Resonance:       0.21
    Distillation:    0.14
    Stabilization:   0.10
    Crystallization: 0.07
    Rest:            0.06

  functional_distribution:
    OBSERVE:   0.09
    FRAME:     0.24
    ABSTRACT:  0.19
    STRUCTURE: 0.18
    TENSION:   0.16
    DECIDE:    0.09
    META:      0.05

  tension_profile:
    common_types: ["overload", "stability_vs_exploration"]
    avg_intensity: 0.61

  natural_language_summary: >
    Over the last two months, the subject frequently enters Divergence
    and Resonance, with moderate use of Distillation and relatively low
    time in Rest. Tension themes center on overload and balancing
    stability versus exploration. Functional activity is dominated by
    framing and abstraction, with a noticeable but not overwhelming
    emphasis on tension-focused moves.
```

This example is illustrative only; implementations are free to adopt different encodings as long as they respect the normative semantics defined in this document.