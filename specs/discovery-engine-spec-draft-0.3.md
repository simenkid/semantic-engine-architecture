# Discovery Engine Specification – Draft 0.3 (Refined)

> **Document ID:** discovery-engine-spec-draft-0.3  
> **Status:** Draft (v0.3, refined core)  
> **Intended Audience:** Architects / researchers / implementers of Semantic-Dynamics-compatible systems.

---

## 0. Core Value

The Discovery Engine (DE) makes a subject’s **thinking pattern discoverable** by turning SD-structured reflection logs into a compact, descriptive artifact:

> **Rhythm Descriptor (RD)** — a summary of how semantic movement typically unfolds for a subject over time.

DE is **descriptive, not prescriptive**:
- It does not decide what the subject *should* think.
- It does not require any specific model architecture.
- It does not change SD semantics; it uses SD as the coordinate system.

---

## 1. Scope and Non-Goals

### 1.1 In Scope
DE MUST be able to:
1) reconstruct semantic flow from SD Logs,  
2) apply functional tagging (what each segment is *doing*),  
3) aggregate into RD artifacts over a declared data scope.

### 1.2 Non-Goals
This spec does NOT define:
- the "best" inference algorithm,
- any runtime that consumes RD to adapt behavior,
- any user interface or storage system.

---

## 2. Terminology

| Term | Definition |
|---|---|
| Subject | The entity being analyzed (person, team, agent). |
| SD Log | A structured record of sessions expressed with SD vocabulary. |
| Session | A coherent reflection / reasoning episode. |
| Mind-pop | Minimal SD semantic emission unit (utterance / note / thought). |
| Thread | Time-ordered sequence of mind-pops revisiting a shared tension/topic. |
| Branch | Set of threads exploring distinct trajectories around the same tension core. |
| Tension Snapshot | Local description of tension types/intensity around a point or segment. |
| SD Phase | Emergence / Divergence / Resonance / Distillation / Stabilization / Crystallization / Rest. |
| Regime | Higher-level SD pattern (e.g., Runaway Divergence, Premature Stabilization, etc.). |
| Tagging Unit | Minimal element to which DE assigns a primary Functional Tag. |
| Functional Tag | Label for the functional role of a Tagging Unit (e.g., FRAME, ABSTRACT). |
| Rhythm Descriptor (RD) | Descriptive summary of a subject’s long-term semantic dynamics. |
| Data Scope | Window of SD Logs used to generate an RD (e.g., last N sessions). |

Normative keywords **MUST / SHOULD / MAY** follow RFC 2119 conventions.

---

## 3. Discovery Engine Responsibilities (Logical)

A conformant DE has three identifiable responsibilities:

1) **Flow Reconstruction**  
2) **Functional Tagging**  
3) **Aggregation into RD**

Implementations MAY merge internal modules, but behavior MUST preserve these responsibilities.

### 3.1 Flow Reconstruction

Given SD Logs, DE:
- orders mind-pops in time,
- associates mind-pops with sessions (and threads/branches if present),
- uses explicit SD annotations (phases/regimes/tension snapshots) when available,
- MAY infer missing phases/regimes, but MUST mark them as inferred.

Normative requirements:
- DE MUST preserve temporal ordering of mind-pops within each session.
- If `thread_id` / `branch_id` exist, DE MUST NOT silently discard them.
- If DE infers `sd_phase` or `regime`, it MUST record label origin: `observed | inferred`.

### 3.2 Functional Tagging

DE:
- segments flow into Tagging Units,
- assigns exactly one **primary** Functional Tag per unit,
- optionally assigns secondary tags and confidence.

Normative requirements:
- Each Tagging Unit MUST reference ≥1 underlying mind-pop (`mp_id`) and a `session_id`.
- Each Tagging Unit MUST have exactly one primary Functional Tag.
- DE SHOULD emit a confidence score for the primary tag when meaningful.

### 3.3 Aggregation into Rhythm Descriptors (RD)

Over a declared Data Scope, DE aggregates:
- SD phase distributions and transitions,
- regime statistics (observed/inferred),
- tension profiles (if present),
- functional tag distributions and transitions (if tagging enabled),

and emits one or more RD artifacts.

Normative requirements:
- Each RD MUST include an explicit `data_scope`.
- If a dimension is missing/sparse (e.g., tension), DE SHOULD omit the field or mark it low-confidence.
- DE MUST not include raw verbatim mind-pop text inside RD by default (privacy-by-default).

---

## 4. Input Data Model (Minimal Contract)

This spec is encoding-agnostic (JSON/YAML/DB are all acceptable).
A conformant DE MUST document how it handles missing fields.

### 4.1 Session (Required Fields)

A Session MUST provide:
- `session_id`
- `subject_id`
- `timestamp_start` (or an equivalent ordering anchor)
- `mind_pops` (ordered list)

Optional:
- `timestamp_end`
- `context` (domain/task/stakes)
- session-level `phase_summary`
- session-level `tension_summary`

### 4.2 Mind-pop (Required Fields)

A Mind-pop MUST provide:
- `mp_id`
- `timestamp` (absolute or relative; must be orderable within session)
- `text` (raw content; may be stored separately but must be referenceable)

Optional:
- `thread_id`, `branch_id`
- `sd_phase` (one of SD phases)
- `regime` (one of SD regimes)
- `tension_snapshot`

### 4.3 Label Origin

If `sd_phase` / `regime` are present, DE SHOULD preserve them as `observed`.
If inferred, DE MUST store origin as `inferred`.

A minimal mechanism:

```yaml
sd_phase:
  value: "Divergence"
  origin: "observed"   # or "inferred"
  confidence: 0.82     # optional
```

---

## 5. Functional Tagging Model

Functional Tags are an analytic axis introduced by DE (not part of SD itself).

### 5.1 Functional Tag Set (Base)

A conformant DE **MUST** support at least:

- `OBSERVE` — gather/revisit inputs, retrieve, read, tool-invoke
- `FRAME` — name/reframe intent/scope/questions
- `ABSTRACT` — extract invariants, schemas, patterns, principles
- `STRUCTURE` — decompose into parts/layers/interfaces
- `TENSION` — surface conflicts/fog/overload/misalignment
- `DECIDE` — commit to direction/choice/next step
- `META` — reflect on the process/rhythm, adjust approach


### 5.2 Tagging Unit (Minimal Schema)

```yaml
unit_id: "u-001"
session_id: "sess-123"
mp_ids: ["mp-01", "mp-02"]   # ≥1
primary_tag: "FRAME"         # exactly one
secondary_tags: ["TENSION"]  # optional
confidence: 0.86             # optional
tension_snapshot:            # optional
  types: ["fog"]
  intensity: 0.6
```

---

## 6. Rhythm Descriptor (RD) v0.3

### 6.1 Purpose

An RD summarizes:
- how the subject occupies and transitions between SD phases,
- how regimes appear and recover (when available),
- how tensions correlate with phases/regimes (when available),
- how functional moves distribute/transition (if tagging enabled).
RD is descriptive and intended to be portable across systems.
RD MAY additionally include **pattern metrics** that capture longer-horizon structure beyond phase/regime/tags,
including `thread_metrics`, `branch_metrics`, and `recovery_patterns` (see Appendix B).


### 6.2 Minimal Fields (MUST)

An RD MUST contain:
- `subject_id`
- `data_scope` – description of the SD Log window used (e.g., "last 40 sessions", date range).
- `phase_distribution` – normalized frequencies over SD phases
(Emergence, Divergence, Resonance, Distillation, Stabilization, Crystallization, Rest).
- `phase_transitions` – aggregated transition statistics between SD phases.
- `regime_statistics` – may be empty if unavailable, but field must exist with explicit availability.
- `tension_profile` – same rule: explicit availability.
- `functional_distribution` – same rule: explicit availability.
- `functional_transitions` – same rule: explicit availability.
- `data_quality` – coverage + inferred-rate

Rationale: early drafting favors **explicit absence** over silent omission.

### 6.3 RD Schema (Suggested)

```yaml
rd_descriptor:
  rd_version: "0.3"
  subject_id: "anon-123"

  data_scope:
    type: "session_count"        # or "date_range"
    value: 40
    from: null
    to: null

  phase_distribution:
    Emergence: 0.18
    Divergence: 0.24
    Resonance: 0.20
    Distillation: 0.16
    Stabilization: 0.10
    Crystallization: 0.07
    Rest: 0.05

  phase_transitions:
    adjacency: "within_session"  # documented choice
    matrix:
      "Emergence->Divergence": { prob: 0.61 }
      "Divergence->Distillation": { prob: 0.42 }

  regime_statistics:
    availability: "partial"      # none|partial|full
    items:
      RunawayDivergence: { freq: 0.12 }
      PrematureStabilization: { freq: 0.05 }

  tension_profile:
    availability: "partial"
    common_types: ["overload", "should_vs_feel"]
    avg_intensity: 0.64

  functional_distribution:
    availability: "full"
    OBSERVE: 0.08
    FRAME: 0.22
    ABSTRACT: 0.18
    STRUCTURE: 0.20
    TENSION: 0.17
    DECIDE: 0.10
    META: 0.05

  functional_transitions:
    availability: "full"
    adjacency: "turn_level"
    matrix:
      "FRAME->ABSTRACT": { prob: 0.28 }
      "ABSTRACT->STRUCTURE": { prob: 0.31 }

  data_quality:
    sessions_used: 40
    mind_pops_used: 620
    phase_label_coverage: 0.40          # observed labels fraction
    phase_inferred_rate: 0.60
    regime_label_coverage: 0.15
    tension_coverage: 0.22
    tagging_granularity: "mind_pop"     # or "segment"

  natural_language_summary: >
    The subject frequently moves into Divergence and Resonance, with moderate
    Distillation and low Rest. Functional activity is dominated by framing and
    structuring, and overload-related tension recurs.

  generation_metadata:
    generated_at: "2025-12-12T00:00:00Z"
    update_mode: "batch"            # batch|incremental
    scope_revision: "r-000012"      # optional but recommended when incremental
    generator: "de"
    generator_version: "0.3.0"
    notes: "Phases inferred when missing; regime inference disabled."

```

### 6.4 Aggregation Requirements

A conformant RD generator:
- MUST declare `data_scope`.
- MUST produce `phase_distribution` normalized (sum ≈ 1.0).
- MUST define and document adjacency semantics for transitions.
- MUST expose inferred vs observed coverage in `data_quality`.

---

## 7. Conformance

### 7.1 Implementation Classes

1. RD-Generating Discovery Engine
    - Consumes SD Logs, performs reconstruction + tagging, outputs RD.
2. RD-Inspecting Tool
    - Consumes RD, visualizes/reports it, may not access raw logs.

### 7.2 Minimal Conformance Profile (RD-Generating)

A minimally conformant DE:
- accepts sessions + mind-pops with timestamps + text,
- assigns Functional Tags at mind-pop granularity,
- produces RD with:
    - `subject_id`, `data_scope`,
    - `phase_distribution`, `phase_transitions`,
    - `functional_distribution`, `functional_transitions`,
    - and `data_quality`.

Regimes and tensions MAY be "availability: none" in early drafts.

--- 

## 8. Privacy and Safety Notes (Short)

Because SD Logs may contain sensitive reflection data:
- RD SHOULD avoid verbatim text by default.
- Implementations SHOULD support pseudonymized `subject_id`.
- Implementations SHOULD provide a human-readable explanation of what RD means.

--- 

## Appendix A — Tiny Worked Example (Informative)

### A.1 Input (Simplified)

```yaml
session_id: "sess-001"
subject_id: "anon-123"
timestamp_start: "2025-11-27T09:00:00Z"

mind_pops:
  - mp_id: "mp-01"
    timestamp: "2025-11-27T09:01:00Z"
    text: "I feel overloaded by too many projects."
    sd_phase: { value: "Emergence", origin: "observed" }

  - mp_id: "mp-02"
    timestamp: "2025-11-27T09:02:30Z"
    text: "Maybe I should list them all and see which ones truly matter."
```

### A.2 Tagging Output (Simplified)

```yaml
tagged_units:
  - unit_id: "u1"
    session_id: "sess-001"
    mp_ids: ["mp-01"]
    primary_tag: "TENSION"
    secondary_tags: ["FRAME"]
    confidence: 0.92

  - unit_id: "u2"
    session_id: "sess-001"
    mp_ids: ["mp-02"]
    primary_tag: "FRAME"
    secondary_tags: ["STRUCTURE"]
    confidence: 0.88
```

### A.3 RD Output (Single-Session, Illustrative)

```yaml
rd_descriptor:
  rd_version: "0.3"
  subject_id: "anon-123"
  data_scope: { type: "session_count", value: 1 }

  phase_distribution:
    Emergence: 0.50
    Divergence: 0.50
    Resonance: 0
    Distillation: 0
    Stabilization: 0
    Crystallization: 0
    Rest: 0

  phase_transitions:
    adjacency: "within_session"
    matrix:
      "Emergence->Divergence": { prob: 1.0 }

  regime_statistics: { availability: "none", items: {} }
  tension_profile: { availability: "none" }
  functional_distribution: { availability: "full", OBSERVE: 0, FRAME: 0.5, ABSTRACT: 0, STRUCTURE: 0, TENSION: 0.5, DECIDE: 0, META: 0 }
  functional_transitions: { availability: "full", adjacency: "turn_level", matrix: { "TENSION->FRAME": { prob: 1.0 } } }

  data_quality:
    sessions_used: 1
    mind_pops_used: 2
    phase_label_coverage: 0.50
    phase_inferred_rate: 0.50
    tension_coverage: 0
    tagging_granularity: "mind_pop"
```

---

## Appendix B — Optional Pattern Metrics (Threads, Branches, Recovery) (Informative)

This appendix defines a **minimal** optional set of fields for capturing longer-horizon structure.
These fields are designed to be useful even when tensions/regimes are sparse.

### B.1 thread_metrics (Optional)

If present, `thread_metrics` SHOULD avoid verbatim mind-pop text and SHOULD reference only IDs.

```yaml
thread_metrics:
  availability: "partial"          # none|partial|full
  origin: "mixed"                  # observed|inferred|mixed
  thread_count: 18
  mean_mind_pops_per_thread: 11.2
  revisit_rate: 0.37               # fraction of mind-pops that belong to a previously-seen thread within scope
  top_threads:
    - thread_id: "t-07"
      mind_pop_count: 42
      sessions_touched: 9
    - thread_id: "t-02"
      mind_pop_count: 31
      sessions_touched: 6
```

Recommended interpretation:
- High `revisit_rate` often indicates persistent themes being revisited (not necessarily “good” or “bad”).
- `origin` clarifies whether threads were logged vs inferred.

### B.2 branch_metrics (Optional)

```yaml
branch_metrics:
  availability: "partial"          # none|partial|full
  origin: "observed"               # observed|inferred|mixed
  branch_count: 5
  mean_threads_per_branch: 3.2
  branching_rate_per_session: 0.18 # average number of new branches per session (within scope)
```

### B.3 recovery_patterns (Optional)

`recovery_patterns` captures **recurring exit motifs** from an undesired state (regime or phase),
expressed as a compact pattern (phase-path and/or functional-path) with frequency.

```yaml
recovery_patterns:
  availability: "partial"          # none|partial|full
  adjacency: "within_session"      # documented choice
  items:
    - trigger:
        regime: "RunawayDivergence"     # optional
        phase: null                     # optional
      recovery:
        phase_path: ["Distillation", "Rest"]     # optional
        functional_path: ["STRUCTURE", "DECIDE"] # optional
      stats:
        freq: 0.16
        median_steps: 3

    - trigger:
        regime: "ChronicOscillation"
      recovery:
        phase_path: ["Resonance", "Distillation"]
      stats:
        freq: 0.09
        median_steps: 4
```

Minimal constraints (recommended):
- Patterns SHOULD be derived from IDs/labels, not raw text.
- `adjacency` MUST be stated if `median_steps` is used.
- If triggers or paths are inferred, implementations SHOULD expose inference in `origin` or in `data_quality`.

---

## Appendix C — Incremental Processing (Batch vs Incremental) (Informative)

DE MAY operate in either:
- **batch** mode: regenerate RD from a fixed scope; or
- **incremental** mode: update RD as new sessions/mind-pops arrive.

### C.1 Minimal rules for incremental mode

If `update_mode: incremental` is used in `generation_metadata`:
- The implementation MUST avoid double-counting (idempotent ingestion by `session_id`/`mp_id`).
- The implementation MUST keep `data_scope` semantics explicit (e.g., sliding window vs growing window).
- Normalized distributions (e.g., `phase_distribution`) MUST be renormalized after updates.
- The implementation SHOULD emit `scope_revision` as a monotonic identifier for the RD evolution.

Recommended minimal metadata (already supported by Patch B):
- `generated_at`
- `update_mode`
- `scope_revision` (recommended)