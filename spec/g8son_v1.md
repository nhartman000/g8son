# G8SON Specification v1

**G8SON — Gate Sequential Object Notation**  
**Author:** Nicholas Hartman / American Milestone Inc.

## 1. Purpose

G8SON defines bounded conditional gates used inside MG8 execution units. A `.g8son` file does not own system state or global orchestration. It evaluates state supplied by `.gst`, returns a bounded result, and exposes enough identity/metadata for `.ork` to route execution and `.qson` to record the event.

## 2. Canonical file bound

A `.g8son` file MUST contain between **1 and 3 gates** inclusive.

This bound is architectural. G8SON files are intended to remain local, reviewable, and composable rather than becoming unbounded decision graphs.

## 3. Identity separation

The format distinguishes:

```text
file_id != gate_id != trace_id
```

- `file_id`: stable identity of the `.g8son` document.
- `gate_id`: stable identity of one gate definition inside that document.
- `trace_id`: runtime identity for one attempted gate execution.

The same gate may be executed multiple times. Every attempt receives a distinct `trace_id`.

## 4. Gate result space

A canonical gate resolves to one of:

```text
PASS
FAIL
INTERMEDIATE
```

### PASS
The gate's required conditions are satisfied under the current state/context.

### FAIL
The gate's required conditions are not satisfied.

### INTERMEDIATE
The gate cannot yet resolve to PASS or FAIL. Additional state, a declared local reasoning step, human decision, override, or routed continuation may be required.

`INTERMEDIATE` MUST NOT be treated as PASS unless a profile explicitly declares that behavior.

## 5. State dependency

G8SON evaluates state; it does not define the canonical state model.

State and contextual constraints belong to `.gst`. A gate may reference values or predicates derived from the active `.gst` state.

Conceptually:

```text
.gst state/context
      ↓
condition evaluation
      ↓
.g8son gate result
      ↓
.ork routing
      ↓
.qson trace
```

## 6. Gate definition

A gate definition SHOULD include:

- `gate_id`
- `order` when order inside the file matters
- `type`
- `conditions` or equivalent bounded requirements
- declared outcome routing or policy

A gate MAY also include:

- precedent/context references,
- bounded dialogue injection,
- local reasoning instructions,
- overrides,
- branch targets,
- human-decision requirements,
- metadata/evidence references.

These optional fields are profile-dependent.

## 7. Gate type

`type` identifies the gate's evaluation behavior. The standard does not require all deployments to implement the same operator catalog.

Examples may include atomic predicates or deterministic operators such as `AND`. Additional operators are profile/runtime concerns and MUST have defined semantics before use.

## 8. Conditions

`conditions` expresses the bounded requirements evaluated by the gate.

Conditions may be represented as simple expressions or structured predicate objects, but a deterministic profile MUST define:

1. how state paths are resolved,
2. what operators mean,
3. how missing values are treated,
4. how multiple conditions combine,
5. what produces `INTERMEDIATE` rather than `FAIL`,
6. whether external/model-assisted evaluation is permitted.

## 9. Sequential semantics

Where multiple gates exist in one file, `order` establishes their local sequence unless the active profile defines an explicit dependency graph.

The one-to-three-gate bound applies regardless of whether gates are sequential, logically composed, or locally branched.

## 10. Branching and override

A gate may declare outcome routing such as:

```json
{
  "PASS": "continue",
  "FAIL": "stop",
  "INTERMEDIATE": "review"
}
```

The destination labels are interpreted by `.ork` or the runtime profile.

Overrides MUST be explicit and auditable. A human or system override should not erase the original gate result; it should create a separate traced decision event.

## 11. Trace contract

Every attempted gate execution MUST have an event-level `trace_id`.

A corresponding `.qson` record SHOULD be able to identify at minimum:

- `trace_id`,
- run/pipeline identity where used,
- `file_id`,
- `gate_id`,
- input-state reference,
- gate result,
- selected branch/action,
- actor/evaluator,
- timestamp or execution index,
- relevant output/evidence metadata.

## 12. Determinism boundary

G8SON can participate in deterministic execution only when the runtime/profile defines deterministic behavior for:

- state canonicalization,
- predicate evaluation,
- operator semantics,
- gate ordering,
- missing/ambiguous inputs,
- branch selection,
- override handling,
- any external model/tool invocation.

If a stochastic model is used inside an atomic gate, that gate's internal inference is not made deterministic merely by storing it in G8SON. Determinism must be provided by the surrounding constraints, evaluation rules, or acceptance criteria.

## 13. Minimal document

```json
{
  "g8son_version": "1.0",
  "file_id": "example.eligibility.001",
  "gates": [
    {
      "gate_id": "object_confidence",
      "order": 1,
      "type": "AND",
      "conditions": [
        "object_detected",
        "confidence > 0.9"
      ],
      "outcomes": {
        "PASS": "continue",
        "FAIL": "stop",
        "INTERMEDIATE": "review"
      }
    }
  ]
}
```

## 14. Scope boundary

The G8SON standard defines the bounded gate file and its execution semantics. It does not fully specify `.gst`, `.ork`, `.qson`, `.mg8`, or `.mg8pk`; those are separate MG8 file-family specifications.
