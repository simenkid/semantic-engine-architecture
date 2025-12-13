# SEA Core Spec – Formal Draft 0.4
Semantic Engine Architecture (SEA)

**Status:** Draft (Normative except Appendix A and B)  
**Date:** 2025-12-09  
**Author:** Simen

---

## 0. Status of This Document

This document is the **SEA Core Specification**, defining the normative foundation of the **Semantic Engine Architecture (SEA)**.

- Sections **1–10** are **normative**.  
- **Appendix A** is **non-normative**, providing an example plugin for illustration only.

This specification is:
- Implementation-neutral  
- Model-agnostic  
- Persona-agnostic  
- Runtime-independent  

SEA Core may be implemented using prompt-based engines, agent-based runtimes, APIs, SDKs, or hybrid systems.

---

## 1. Introduction

The **Semantic Engine Architecture (SEA)** defines a formal structure for semantic modeling, non-linear reasoning, and tension-driven cognitive processes.

SEA originated from the observation that **co-modeling**—the act of humans and AI jointly constructing models—fails without a stable cognitive substrate:

- Semantic interpretations drift  
- Abstractions collapse unpredictably  
- Structural boundaries dissolve  
- Tension and contradictions remain hidden  
- Reasoning becomes ad hoc and non-repeatable  

SEA addresses these issues by providing:

1. **A minimal, normative set of cognitive operators**  
2. **A non-linear sequencing model** driven by tension  
3. **A profile layer** for personalization  
4. **A plugin architecture** for extending cognitive abilities  

SEA is **not** a prompt, not a persona, and not a behavior style.  
SEA is a **specification defining what a semantic reasoning engine must do**.

---

## 2. Terminology

| Term | Definition |
|------|------------|
| **SEA Core** | The normative, immutable specification defined here. |
| **SEA Runtime (Engine)** | A system that executes Operators according to SEA Core semantics. |
| **Operator (OP)** | A named cognitive function with defined inputs/outputs. |
| **Sequencer** | Component that determines the next Operator based on Tension signals and the active Rhythm Specification. |
| **Tension** | Any ambiguity, conflict, constraint, or trade-off affecting progression. |
| **Cognitive State (CS)** | The Runtime’s current reasoning posture, minimally including active Operator, relevant artifacts (models, structures, Tension), and any active rhythm / mode tags. |
| **Functional Zone** | A conceptual grouping of cognitive roles within the Cognitive State space (e.g., semantic, abstract, structural, tension, decision, observe, meta-reflect). Functional Zones are used to describe *what kind of work* is being done, independent of any specific Persona or task. |
| **Core Functional Layers** | The five canonical Functional Zones defined in §4 that MUST remain representable in all conformant Runtimes: Semantic Interpretation, Abstract Modeling, Layered Structuring, Tension Analysis, Decision Synthesis. |
| **Auxiliary Functional Zones** | Recognized but non-mandatory Functional Zones (e.g., Observe, Meta-Reflect) that MAY appear in RhythmSpecs and internal Runtime representations, but are not required for minimal SEA Core conformance in v0. |
| **Rhythm Specification (RhythmSpec)** | A declarative description of allowed Cognitive States and transitions among Operators, including initial state, transition conditions, and termination conditions. |
| **SEA Profile** | Personalization layer that adjusts reasoning tendencies (e.g., biases, weightings) without altering SEA Core semantics or Operator definitions. |
| **SEA Plugin** | Optional module introducing new Operators into the SEA Runtime. |
| **Plugin Lifecycle** | Required state machine that governs plugin registration, validation, activation, execution, and deactivation. |

**Non-normative note:**  
SEA Core does not prescribe how a RhythmSpec is obtained. It MAY be authored by humans, learned from data, or discovered by upstream systems (e.g., a "Discovery Engine"). SEA only defines how a Runtime must execute a RhythmSpec once one is provided.


---

## 3. Architecture Overview

```text
+------------------------------+
|      SEA Plugins (Optional)  |
+------------------------------+
|        SEA Profile Layer     |
+------------------------------+
|       SEA Runtime Engine     |
+------------------------------+
|         SEA Core Spec        |
+------------------------------+
```

### 3.1 Layering Rules (Normative)

- Plugins MUST depend on the Runtime.
- Profiles MUST depend on the Runtime.
- Runtime MUST depend on SEA Core.
- SEA Core MUST NOT depend on any upper layer.
- Plugins MUST NOT modify Core semantics.
- Profiles MUST NOT add new Operators.

This ensures a **clean, non-inverted dependency graph** and preserves SEA Core stability.

## 4. SEA Core Cognitive Model

SEA defines an initial canonical set of five conceptual cognitive layers:

1. Semantic Interpretation Layer  
2. Abstract Modeling Layer  
3. Layered Structuring Layer  
4. Tension Analysis Layer  
5. Decision Synthesis Layer  

These layers are intended as a **minimal, non-exhaustive basis** for describing
reasoning over complex problems. They:

- Are non-linear.  
- Have no fixed pipeline order.  
- MUST allow re-entry into any layer.  
- MUST allow iterative and tension-driven traversal.  

Runtimes MAY introduce additional internal sub-layers or alternative
decompositions, as long as:

- The functional roles covered by the five canonical layers remain representable, and  
- The overall Cognitive State space remains compatible with RhythmSpec semantics.

---

### 4.1 Cognitive State Space

These layers collectively define a **canonical cognitive state space** for SEA:

- A Cognitive State (CS) captures *where* in this space the Runtime currently is,  
  including (at minimum) the active Operator, the currently relevant semantic artifacts  
  (Abstract Models, Layered Structures, Tension Fields, Decisions), and any runtime mode tags.
- Operators read from and write to this state space.
- The Sequencer inspects Cognitive State and Tension to choose subsequent Operators.

SEA Core does **not** assume that any particular human or Persona traverses this space
in a fixed order. Different Rhythms (e.g., Semantic → Abstract → Layer → Tension → Decision)
MAY be expressed as different **RhythmSpecs** defined over the same canonical state space.
SEA Core only requires that:

- the state space is available to the Runtime, and  
- state transitions follow an explicit RhythmSpec rather than an implicit, hard-coded pipeline.

---

### 4.2 Core and Auxiliary Functional Zones

SEA v0 distinguishes between:

- **Core Functional Layers** – the five canonical layers defined above; and  
- **Auxiliary Functional Zones** – additional, recognized cognitive roles
  that are useful for RhythmSpec design and future evolution, but are not
  required for minimal Runtime conformance.

#### 4.2.1 Core Functional Layers (Recap)

The five Core Functional Layers are:

1. **Semantic Interpretation**  
   - Interpreting and framing what is being talked about and why it matters.

2. **Abstract Modeling**  
   - Compressing situations into models, patterns, and conceptual structures.

3. **Layered Structuring**  
   - Carving situations into layers, components, interfaces, and responsibilities.

4. **Tension Analysis**  
   - Surfacing and classifying tensions: conflicts, ambiguity, overload,
     trade-offs, misalignment, constraints.

5. **Decision Synthesis**  
   - Integrating models, structures, and tensions into committed decisions or
     strategic moves.

A conformant Runtime MUST ensure that these five functional roles remain
representable within its Cognitive State space, even if it chooses to implement
additional internal sub-layers or alternative decompositions.

#### 4.2.2 Auxiliary Functional Zones in v0

SEA v0 recognizes, but does not require, at least the following Auxiliary
Functional Zones:

1. **Observe Zone**  
   - Role: Gathering or revisiting raw material for reasoning.  
   - Examples (non-normative):
     - Reading or re-reading user input or prior conversation segments.  
     - Retrieving documents, examples, or external references.  
     - Calling external tools (e.g., search, code execution) to obtain facts.  
   - Normative status:
     - Runtimes MAY represent Observe-oriented states in their Cognitive State
       space and RhythmSpecs.  
     - SEA Core v0 does **not** prescribe tool semantics or IO mechanisms; such
       details are considered implementation-specific or Plugin-level concerns.

2. **Meta-Reflect Zone**  
   - Role: Reflecting on the adequacy of the current reasoning *process* or
     Rhythm, rather than only on the content being reasoned about.  
   - Examples (non-normative):
     - Questioning whether the current approach, granularity, or framing is
       appropriate.  
     - Deciding to restart from a different framing or to switch to a different
       RhythmSpec.  
   - Normative status:
     - Runtimes MAY represent Meta-Reflect states in their Cognitive State space
       and RhythmSpecs.  
     - SEA Core v0 does **not** require that Meta-Reflect be able to modify
       RhythmSpecs dynamically; any such meta-rhythm behavior, if present,
       MUST be explicitly governed by higher-order rules and is considered
       an advanced feature.

#### 4.2.3 Relationship to RhythmSpec and Conformance

- RhythmSpecs MAY define states that correspond to Auxiliary Functional Zones
  (e.g., `OBSERVE`, `META_REFLECT`), alongside states mapped to Core Functional
  Layers.  
- A conformant Runtime:
  - MUST be able to represent and execute RhythmSpecs that refer only to the
    five Core Functional Layers; and  
  - MAY choose to support additional states associated with Auxiliary Functional
    Zones, but such support is not required for minimal conformance in v0.

This design allows SEA v0 to:

- Standardize a minimal, widely applicable basis (the five Core Functional
  Layers); while  
- Explicitly reserving conceptual "slots" for Observe and Meta-Reflect style
  behavior, so that future revisions and Discovery Engines can make use of them
  without breaking compatibility.


---

## 5. Minimal Operator Set (Normative)

A SEA Runtime **MUST** implement all Operators defined in this section.
Operators MUST NOT be removed, renamed, or redefined.

---

### 5.1 OP: CoModel

**Purpose:**
Transform semantic intent into an explicit abstract model.

**Inputs:**
- Semantic Intent
- Context (optional)

**Outputs:**
- Abstract Model

**Requirements:**
- MUST surface semantic ambiguities where present.
- MUST infer implicit constraints when reasonably derivable from context.
- MUST preserve semantic integrity between input intent and produced model.

---

### 5.2 OP: CoStructure

**Purpose:**
Organize an abstract model into layered structures.

**Inputs:**
- Abstract Model

**Outputs:**
- Layered Structure (boundaries, flows, responsibilities)

**Requirements:**
- MUST detect coupling and critical dependencies.
- MUST define explicit boundaries and responsibility areas.
- MUST identify abstraction-level inconsistencies (e.g., mixed layers).

---

### 5.3 OP: TensionAnalysis

**Purpose:**
Generate the tension landscape for the current model and structure.

**Inputs:**
- Abstract Model
- Layered Structure
- Constraints and Context

**Outputs:**
- Tension Field

**Requirements:**
- MUST classify tensions (conflicts, trade-offs, ambiguities, constraints).
- MUST surface contradictions rather than suppress them.
- MUST identify trade-offs relevant to decision-making.

---

### 5.4 OP: RhythmTransition

**Purpose:**  
Select the next Operator based on the Tension Field, current Cognitive State,  
and the active Rhythm Specification.

**Inputs:**

- Tension Field  
- Current Cognitive State  
- Active RhythmSpec (implicit Runtime context)

**Outputs:**

- Next Operator Identifier

**Requirements:**

- MUST treat the active RhythmSpec as the primary source of allowable transitions  
  between Operators and Cognitive States.
- MUST support arbitrary non-linear sequencing among Operators, as permitted by the
  active RhythmSpec.
- MUST support re-entry into any Operator, as permitted by the active RhythmSpec.
- MUST support Operator skipping, as permitted by the active RhythmSpec.
- MUST expose, at least internally, the transition rationale  
  (e.g., which RhythmSpec rule and which Tension condition were applied).
- MUST NOT encode Persona-specific or hard-coded Operator sequences that bypass
  or contradict the active RhythmSpec.
- MUST NOT enforce a rigid, fixed pipeline independent of RhythmSpec.


---

## 6. Sequencing Model (Normative)

SEA Runtimes MUST implement a Non-Linear Cognitive Sequencer.

---

### 6.1 Sequencer Responsibilities

The Sequencer MUST:

- Use the Tension Field as a primary driver for progression.
- Permit arbitrary transitions between Operators.
- Support multi-pass and looped execution of Operators.
- Expose, at least internally, the rationale for Operator selection.
- Accept Profile-driven bias parameters.
- Maintain semantic and structural continuity during transitions.

The Sequencer MUST NOT:

- Assume or enforce a fixed ordering of Operators.
- Degrade into a purely linear pipeline.

---

### 6.2 Rhythm Specification & Execution Semantics

A SEA Runtime MUST support at least one **Rhythm Specification (RhythmSpec)**.

A RhythmSpec is a declarative structure that defines:

- **State Set** – a finite set of named Cognitive States.  
  Each state MAY bind:
  - one or more Operators to be invoked, and  
  - optional mode tags (e.g., exploration, distillation, rest).
- **Transition Set** – a finite set of allowed transitions between states,  
  each transition MAY include:
  - Tension-based conditions (e.g., intensity thresholds, tension types),  
  - Profile-based conditions (e.g., preferences, biases), and  
  - Contextual conditions defined by the Runtime.
- **Initial State** – the state in which execution begins.
- **Termination Conditions** – conditions under which a reasoning episode is considered complete
  (e.g., Tension below a threshold, explicit user stop signal, or explicit "decision reached").

Normative requirements:

- A Runtime MUST be able to load a RhythmSpec at initialization time.
- A Runtime MAY support dynamic switching between RhythmSpecs, but such switching
  MUST itself be governed by explicit rules (e.g., meta-level transitions).
- The Sequencer and RhythmTransition Operator MUST consult the active RhythmSpec
  when choosing the next Operator.
- When no explicit RhythmSpec is provided, the Runtime MAY use a default RhythmSpec,
  but such a default MUST still be representable in the same formal structure
  (i.e., as a State Set, Transition Set, Initial State, and Termination Conditions).
- SEA Core does not constrain *how* RhythmSpecs are authored or learned; it only
  constrains how they are represented and executed once present.

Non-normative examples of RhythmSpecs include:

- A "Simen-style" rhythm that favors  
  Semantic → Abstract → Layered Structuring → Tension → Decision;
- A "fast decision" rhythm that prioritizes shallow modeling and quick Tension checks;
- A "reflection-heavy" rhythm that loops between Tension Analysis and Abstract Modeling
  before any Decision Synthesis is attempted.


---

## 7. SEA Profile Interface (Normative)

A SEA Runtime MUST accept and apply a SEA Profile.

---

### 7.1 Profile Scope

A Profile MAY define:
- Cognitive preferences (e.g., depth-first vs. breadth-first tendencies).
- Value or tension weighting (which tensions to prioritize).
- Structural tendencies (e.g., favoring modularization or centralization).
- Abstraction scaling habits (e.g., default abstraction level).
- Communication or expression preferences.

---

### 7.2 Profile Constraints

Profiles MUST NOT:
- Introduce new Operators.
- Alter the semantics of SEA Core Operators.
- Break sequencing constraints or bypass the Sequencer.

Profiles MAY:
- Influence rhythm bias (how RhythmTransition selects the next Operator).
- Adjust how different types of tensions are weighted in decision-making.
- Adjust structural sensitivity (e.g., sensitivity to coupling vs. layering errors).

---

## 8. SEA Plugin Architecture (Normative)

SEA Plugins provide optional additional cognitive Operators beyond the SEA Core.

---

### 8.1 Plugin Requirements

Plugins MUST:
- Declare the set of Operators they introduce.
- Declare input/output contracts for each Operator.
- Declare safety and boundary rules.
- Undergo validation before activation.

Plugins MUST NOT:
- Modify SEA Core semantics.
- Override or shadow Core Operators.
- Break Sequencer integrity or interfere with required transitions.

Plugins MAY:
- Provide new cognitive capabilities (e.g., representation, analysis, transformation).
- Interact with SEA Runtime and Profiles via well-defined interfaces.
- Register multiple Operators as part of a single plugin.

---

## 9. Plugins and Extensions (Normative)

SEA provides a plugin mechanism for extending cognitive capabilities without
modifying SEA Core semantics.

### 9.1 Purpose of Plugins

A **SEA Plugin** is an extension module that:

- Introduces one or more additional Operators; and/or  
- Provides specialized implementations of existing Operators for specific domains; and/or  
- Offers auxiliary services (e.g., logging, visualization) that do not alter
  Operator semantics.

Plugins MUST NOT:

- Change the semantics of SEA Core Operators.  
- Bypass the Sequencer or RhythmSpec when influencing reasoning flow.  
- Mutate SEA Core data structures in ways that contradict this specification.

### 9.2 Plugin-Defined Operators

A Plugin MAY declare one or more **Plugin Operators**. Each Plugin Operator:

- MUST have a unique name within the Runtime.  
- MUST specify:
  - Input contracts (required artifacts from the Cognitive State).  
  - Output contracts (artifacts to be written back to the Cognitive State).  
  - Preconditions for safe invocation.  

A Plugin Operator:

- MAY read from the Tension Field.  
- MAY write new Tension signals, but MUST NOT silently discard existing Tension
  unless explicitly authorized by the Runtime configuration.  
- MUST be invocable through the same Operator invocation model as SEA Core
  Operators (i.e., from within the Sequencer / RhythmTransition, not via
  hidden side-channels).

### 9.3 Plugins and Rhythm Specifications

Plugins interact with **RhythmSpecs** in the following way:

- A RhythmSpec MAY reference Plugin Operators as valid targets within its
  State Set and Transition rules.  
- If a RhythmSpec references a Plugin Operator, the Runtime MUST ensure that:
  - The corresponding Plugin is in the ACTIVATED state (see §9.4), and  
  - The Operator’s preconditions are satisfied before invocation.

Normative requirements:

- A Plugin MUST declare which Cognitive States and/or Rhythm modes it is
  compatible with (e.g., "usable during Tension Analysis and Abstract Modeling").
- A Runtime MUST prevent a RhythmSpec from invoking Plugin Operators that are:
  - Not registered, or  
  - Not validated, or  
  - Not activated.

A Plugin MUST NOT alter a RhythmSpec at runtime unless:

- The Runtime explicitly exposes a safe mechanism for Rhythm modification; and  
- Such modifications are themselves governed by explicit, higher-order rules
  (e.g., a meta-level RhythmSpec for "rhythm switching").

### 9.4 Plugin Lifecycle

All Plugins MUST conform to the following lifecycle states:

1. **REGISTERED**  
   - The Plugin is known to the Runtime by identifier and declared capabilities,  
     but has not yet been validated.

2. **VALIDATED**  
   - The Runtime has verified:
     - Structural correctness of Plugin metadata, and  
     - Compatibility with SEA Core version and any declared dependencies.  

3. **ACTIVATED**  
   - The Plugin is eligible for use in reasoning episodes.  
   - Its Operators MAY be invoked by the Sequencer in accordance with the active
     RhythmSpec.

4. **EXECUTED**  
   - The Plugin has been invoked at least once within a reasoning episode.  
   - Execution MUST be logged at least at the level of:
     - Operator name,  
     - Input/output artifact references,  
     - Relevant Tension changes.

5. **DEACTIVATED**  
   - The Plugin is no longer eligible for invocation in new reasoning episodes.  
   - Existing logs and artifacts remain available for inspection.

Normative requirements:

- A Runtime MUST NOT invoke a Plugin Operator before the Plugin reaches
  ACTIVATED state.  
- Deletion or deactivation of a Plugin MUST NOT retroactively alter historical
  reasoning traces.  
- A Plugin MAY be upgraded, but such upgrades MUST either:
  - Preserve Operator names and contracts, or  
  - Be treated as a new Plugin identity with its own lifecycle.


---

## 10. Conformance Requirements (Normative)

This section defines what it means to conform to the SEA Core Specification.

Conformance is defined for:

1. SEA Runtimes (Engines)  
2. SEA Plugins

---

### 10.1 Conformant SEA Runtime

A **SEA Runtime** is conformant with SEA Core if and only if it satisfies all
of the following requirements.

#### 10.1.1 Core Semantics

A conformant Runtime:

1. **Implements the SEA Core Cognitive State Space**  
   - MUST maintain a Cognitive State (CS) as defined in §4.1.  
   - MUST allow Operators to read from and write to this state space.

2. **Implements the required Core Operators**  
   - MUST provide implementations for all SEA Core Operators defined in §5  
     (e.g., CoModel, CoStructure, TensionAnalysis, RhythmTransition, etc.).  
   - These implementations MUST respect the input/output and behavioral
     contracts specified in §5.

3. **Implements a Non-Linear Sequencer**  
   - MUST implement a Sequencer as defined in §6.1.  
   - MUST use Tension as a primary driver of progression.  
   - MUST allow non-linear, re-entrant traversal of Operators.

4. **Implements a Tension Field**  
   - MUST maintain a Tension Field accessible to Operators and the Sequencer.  
   - MUST allow Operators to contribute to, refine, or resolve Tension in a
     traceable way.

#### 10.1.2 Rhythm Specification Support

A conformant Runtime:

1. **RhythmSpec Representation**

   - MUST support at least one **RhythmSpec** as defined in §6.2.  
   - MUST be able to represent any default or built-in reasoning flow as a
     RhythmSpec instance (i.e., a State Set, Transition Set, Initial State,
     and Termination Conditions).

2. **RhythmSpec Execution**

   - MUST ensure that the Sequencer and RhythmTransition Operator consult the
     active RhythmSpec when selecting the next Operator.  
   - MUST NOT hard-code Persona-specific or author-specific pipelines that
     bypass or contradict the active RhythmSpec.

3. **Default RhythmSpec**

   - MAY provide a default RhythmSpec when no explicit one is supplied.  
   - Such a default MUST:
     - Be expressible in the same formal structure as any other RhythmSpec; and  
     - Be replaceable by a user-provided or system-generated RhythmSpec without
       requiring changes to the Runtime’s implementation.

4. **Persona Independence**

   - MUST treat Persona or Profile information (if present) as external to
     SEA Core semantics.  
   - Persona / Profile MAY bias Tension computation or transition preferences,  
     but MUST NOT alter the formal structure of RhythmSpec or the semantics
     of Core Operators.

#### 10.1.3 Profile Handling

A conformant Runtime:

- MAY support one or more **SEA Profiles**.  
- If Profiles are supported, the Runtime:
  - MUST clearly separate Profile effects from Core Operator semantics.  
  - MUST document which aspects of Tension computation or transition biasing
    are influenced by Profiles.  
  - MUST NOT allow Profiles to redefine Operator contracts or RhythmSpec
    structure.

#### 10.1.4 Plugin Architecture

If a Runtime supports Plugins, it:

- MUST implement the Plugin lifecycle as defined in §9.4.  
- MUST ensure that:
  - Only VALIDATED and ACTIVATED Plugins are eligible for Operator invocation.  
  - Plugin Operators are invocable only through the Sequencer / RhythmTransition
    according to the active RhythmSpec.  
- MUST prevent Plugins from:
  - Overriding Core Operator semantics.  
  - Modifying SEA Core data structures in ways that violate this specification.

---

### 10.2 Conformant SEA Plugin

A **SEA Plugin** is conformant with SEA Core if and only if it satisfies all
of the following:

1. **Declaration**

   - Declares a unique Plugin identifier.  
   - Declares all Plugin Operators, including:
     - Names,  
     - Input/output contracts,  
     - Compatible Cognitive States / Rhythm modes.

2. **Lifecycle Compliance**

   - Participates in the lifecycle defined in §9.4.  
   - Does not request execution before reaching the ACTIVATED state.

3. **Semantic Integrity**

   - Does NOT redefine the semantics of SEA Core Operators.  
   - Any overlap in naming or functionality is explicitly documented and
     does not conflict with Core Operator contracts.

4. **RhythmSpec Compatibility**

   - Does NOT assume hard-coded sequencing that bypasses RhythmSpec.  
   - If it proposes modifications to RhythmSpec, such proposals are treated as
     data and are subject to explicit acceptance rules defined by the Runtime.

---

### 10.3 Non-Goals and Out-of-Scope

For clarity, SEA Core conformance:

- Does NOT require any specific implementation technology  
  (e.g., does not mandate LLMs, symbolic engines, or any particular stack).  
- Does NOT require support for any particular Persona, Profile, or use case.  
- Does NOT cover upstream systems that may:
  - Discover RhythmSpecs (e.g., a Discovery Engine), or  
  - Generate Self-Models or Profiles.

Such systems are considered **consumers** or **producers** of SEA-compatible
artifacts, but are not themselves required for SEA Core conformance.


---

## Appendix A — Non-Normative Example Plugin: NarrativeSimplify

**Status:** Non-Normative Example
**Plugin Name:** NarrativeSimplify
**Type:** Representation & Communication Plugin

This appendix is informative and does not form part of the normative SEA Core.
It demonstrates how SEA Plugins can introduce human-intuitive cognitive abilities.

---

### A.1 OP: NarrativeSimplify

**Purpose:**
Transform complex cognitive artifacts (models, structures, tensions, decisions) into simplified, narrative-form explanations suitable for oral communication or presentation.

**Inputs:**
- Abstract Model
- Layered Structure
- Tension Field (optional)
- Any other SEA cognitive artifact produced by Core Operators or other Plugins

**Outputs:**
- Narrative-form explanation
- Oral-style simplified description
- Presentation-friendly rendering
- Multi-layer narrative summaries (e.g., technical, general audience, minimal)

**Behavioral Requirements:**
- MUST preserve semantic accuracy and core meaning.
- MUST provide, at least implicitly, the rationale for simplification.
- MAY reduce abstraction complexity to match the intended audience.
- MUST NOT override or modify SEA Core Operators or their semantics.
- MAY generate multiple narrative layers according to context or Profile.

---

### A.2 Interaction with SEA Runtime

Once activated, the NarrativeSimplify Operator becomes part of the operator graph and is available to the Sequencer.

The Sequencer MAY select NarrativeSimplify when:
- Tension indicates a need for external communication or explanation.
- A Profile requests simplified or narrative-form outputs.
- The current reasoning state is too abstract or dense for direct presentation.

NarrativeSimplify therefore:
- Illustrates how SEA Plugins add new capabilities rather than altering existing ones.
- Clarifies the difference between Profiles (preferences) and Plugins (abilities).

---

### A.3 Non-Normative Nature

NarrativeSimplify is provided:
- As an example of a human-intuitive cognitive ability that can be modeled as a plugin.
- To demonstrate Plugin declaration, usage, and lifecycle in a concrete way.
It is not required for SEA Core conformance.

## Appendix B – Example RhythmSpec: "Simen Default Rhythm"
*(Non-normative)*

This appendix provides a non-normative example of a Rhythm Specification
(**RhythmSpec**) that approximates the default cognitive rhythm observed for
Simen during early co-modeling work.

It is intended as:

- A **worked example** of how to encode a rhythm over the SEA cognitive state space.
- A **default preset** that a Runtime MAY choose to ship with, but MUST NOT
  hard-code outside the RhythmSpec mechanism.
- A **reference pattern** for comparison with other, differently shaped rhythms.

This appendix is informative only. It does **not** add new conformance
requirements beyond those already defined in §6 and §10.

---

### B.1 Informal Description

The "Simen Default Rhythm" can be summarized as:

> **Frame Semantics → Abstract Modeling → Layered Structuring → Tension Analysis → Decision Synthesis**,  
> with non-linear re-entry from Tension Analysis back into any prior phase,  
> and occasional meta-reflective loops.

In words:

1. **Frame Semantics** – clarify what is being talked about and why it matters
   (naming, reframing, surfacing intent and scope).
2. **Abstract Modeling** – compress the situation into conceptual models,
   patterns or invariants.
3. **Layered Structuring** – carve the situation into layers, components, and
   interfaces; identify responsibilities and boundaries.
4. **Tension Analysis** – surface contradictions, trade-offs, overload, and
   "fog", and determine where the true pressure lies.
5. **Decision Synthesis** – commit to a structural or strategic move, often in
   the form of a re-architecture, plan, or principle.

Tension plays a central role:

- If Tension reveals **misframing**, the rhythm tends to jump back to
  **Frame Semantics**.
- If Tension reveals **pattern gaps or over-generalization**, it tends to jump
  back to **Abstract Modeling**.
- If Tension reveals **boundary conflicts or coupling issues**, it tends to
  jump back to **Layered Structuring**.
- If Tension is sufficiently reduced and a decision surface is clear, the rhythm
  proceeds to **Decision Synthesis**.

---

### B.2 State Set

This RhythmSpec uses the following Cognitive States:

- `FRAME_SEMANTIC`  
  - Primary Operator(s): `CoModel` (framing mode)  
  - Mode tags: `["frame", "semantic", "intent"]`  
  - Role: Name/reframe the problem, surface intent, clarify vocabulary and scope.

- `ABSTRACT_MODELING`  
  - Primary Operator(s): `CoModel` (abstraction mode)  
  - Mode tags: `["abstract", "pattern", "modeling"]`  
  - Role: Extract patterns, invariants, and conceptual models from examples
    and raw narratives.

- `LAYERED_STRUCTURING`  
  - Primary Operator(s): `CoStructure`  
  - Mode tags: `["structure", "layering", "boundary"]`  
  - Role: Define layers, components, interfaces, and responsibility splits.

- `TENSION_ANALYSIS`  
  - Primary Operator(s): `TensionAnalysis`  
  - Mode tags: `["tension", "conflict", "diagnosis"]`  
  - Role: Aggregate and analyze Tension signals (ambiguity, overload,
    conflict, trade-offs, misalignment).

- `DECISION_SYNTHESIS`  
  - Primary Operator(s): `DecisionSynthesis`  
  - Mode tags: `["decision", "synthesis", "commit"]`  
  - Role: Synthesize structural or strategic decisions, given the current
    models, structures, and Tension landscape.

- `META_REFLECT` *(optional, low-frequency)*  
  - Primary Operator(s): `CoModel` (meta mode), optional plugins  
  - Mode tags: `["meta", "reflection"]`  
  - Role: Reflect on the adequacy of the current rhythm itself, not just the
    content being reasoned about. This state is an example of a Meta-Reflect
    Auxiliary Functional Zone as described in §4.2.2.


---

### B.3 Example RhythmSpec (YAML)

The following YAML example illustrates how the "Simen Default Rhythm" MAY be
expressed as a RhythmSpec instance.

This is an **illustrative format** only. SEA Core constrains the *structure*
(State Set, Transition Set, Initial State, Termination Conditions) but does
not mandate any particular serialization.

```yaml
id: "rhythm-simen-default-v0.1"
version: "0.1"
description: >
  A non-linear, tension-driven reasoning rhythm approximating Simen's observed
  pattern: Frame Semantics -> Abstract Modeling -> Layered Structuring ->
  Tension Analysis -> Decision Synthesis, with re-entry via Tension.

states:
  - name: FRAME_SEMANTIC
    operators: ["CoModel"]
    mode_tags: ["frame", "semantic", "intent"]

  - name: ABSTRACT_MODELING
    operators: ["CoModel"]
    mode_tags: ["abstract", "pattern", "modeling"]

  - name: LAYERED_STRUCTURING
    operators: ["CoStructure"]
    mode_tags: ["structure", "layering", "boundary"]

  - name: TENSION_ANALYSIS
    operators: ["TensionAnalysis"]
    mode_tags: ["tension", 'diagnosis', 'conflict']

  - name: DECISION_SYNTHESIS
    operators: ["DecisionSynthesis"]
    mode_tags: ["decision", "synthesis", "commit"]

  - name: META_REFLECT
    operators: ["CoModel"]
    mode_tags: ["meta", "reflection"]

initial_state: FRAME_SEMANTIC

transitions:
  # 1) Typical forward progression

  - from: FRAME_SEMANTIC
    to: ABSTRACT_MODELING
    condition:
      type: "tension_based"
      when:
        - primary_tension.type in ["vagueness", "ambiguity"]
        - frame_clarity >= 0.6

  - from: ABSTRACT_MODELING
    to: LAYERED_STRUCTURING
    condition:
      type: "model_stability"
      when:
        - model_stability >= 0.5
        - need_structure_for_action == true

  - from: LAYERED_STRUCTURING
    to: TENSION_ANALYSIS
    condition:
      type: "always"
      when: []

  - from: TENSION_ANALYSIS
    to: DECISION_SYNTHESIS
    condition:
      type: "residual_tension"
      when:
        - residual_tension.level <= 0.4
        - decision_surface_ready == true

  # 2) Re-entry transitions from TensionAnalysis

  - from: TENSION_ANALYSIS
    to: FRAME_SEMANTIC
    condition:
      type: "misframing_detected"
      when:
        - primary_tension.type == "misframing"

  - from: TENSION_ANALYSIS
    to: ABSTRACT_MODELING
    condition:
      type: "pattern_gap"
      when:
        - primary_tension.type in ["pattern_gap", "over_generalization"]

  - from: TENSION_ANALYSIS
    to: LAYERED_STRUCTURING
    condition:
      type: "structural_conflict"
      when:
        - primary_tension.type in ["boundary_conflict", "coupling_issue"]

  # 3) Post-decision checks

  - from: DECISION_SYNTHESIS
    to: TENSION_ANALYSIS
    condition:
      type: "post_decision_tension"
      when:
        - post_decision_tension.level > 0.3

  # 4) Meta-reflection

  - from: TENSION_ANALYSIS
    to: META_REFLECT
    condition:
      type: "meta_question"
      when:
        - meta_tension.level >= 0.7

  - from: META_REFLECT
    to: FRAME_SEMANTIC
    condition:
      type: "restart_from_frame"
      when:
        - meta_decision == "reframe"

termination:
  - condition:
      type: "low_tension_and_decision"
      when:
        - residual_tension.level <= 0.2
        - decision_committed == true
        - explicit_stop_requested == true
```

**Notes:**

- The `when` clauses are illustrative. A concrete Runtime MAY implement these as:
  - threshold checks over numeric Tension measures,
  - symbolic predicates,
  - or a combination thereof.
- The presence of `META_REFLECT` is optional; a Runtime MAY ignore this state or
treat it as a rarely used escape hatch for rhythm introspection.

---

### B.4 Tension Usage in This Rhythm

In this RhythmSpec, Tension serves at least four roles:

1. Progression Trigger
  - Movement from `FRAME_SEMANTIC` to `ABSTRACT_MODELING` is driven by tension
associated with vagueness, once framing is "good enough".

2. Diagnostic Router
  - From `TENSION_ANALYSIS`, the type of primary Tension determines whether
to re-enter framing, abstraction, or structuring.

3. Decision Gate
  - Transition to `DECISION_SYNTHESIS` is gated on residual Tension being low
enough and a decision surface being sufficiently clear.

4. Meta-Stability Check
  - Post-decision Tension can push the rhythm back into `TENSION_ANALYSIS`,
preventing premature closure when unresolved conflicts remain.

This illustrates one way in which a RhythmSpec can formalize "tension-driven"
sequencing without hard-wiring any specific Persona into SEA Core.

---

### B.5 Relationship to SEA Core

This RhythmSpec:
- Uses only the canonical cognitive state space defined in §4
(Semantic Interpretation, Abstract Modeling, Layered Structuring,
Tension Analysis, Decision Synthesis), plus an optional meta-reflection state.
- Treats all sequencing as explicit transitions, not as an implicit pipeline.
- Can be enabled, disabled, or swapped out purely by changing the active
RhythmSpec—without modifying the SEA Runtime implementation.

A conformant SEA Runtime:
- MAY ship this RhythmSpec as a **default preset**;
- MUST still treat it as just another RhythmSpec, expressible in the same
format as any user- or system-generated rhythm;
- MUST NOT encode this particular rhythm as a special case that bypasses
the general RhythmSpec execution semantics.