# G8SON Architecture

**Gate Sequential Object Notation**  
Nicholas Hartman / American Milestone Inc.

## 1. Position in the MG8 file family

```text
.gst state/context
      ↓
.g8son gate/operator
      ↓
PASS / FAIL / INTERMEDIATE
      ↓
.ork routing / sequencing
      ↓
.qson event trace
```

A `.g8son` file contains **1–3 gates maximum**.

## 2. Three identity layers

G8SON explicitly separates:

```text
file_id
  ≠
gate_id
  ≠
trace_id
```

This matters because a gate definition may be executed repeatedly. The definition remains the same while each execution occurrence receives a new `trace_id`.

## 3. Runtime relationship

Conceptually:

```text
load active .gst
      ↓
resolve gate definition
      ↓
evaluate bounded conditions
      ↓
PASS / FAIL / INTERMEDIATE
      ↓
apply explicit routing policy
      ↓
write .qson trace event
```

The `.g8son` file does not itself own global execution order; `.ork` determines when the gate is evaluated and what the declared outcome destination means.

## 4. Local bounded reasoning

A gate can be richer than a Boolean primitive. A gate may carry:

- conditions,
- precedent/context references,
- bounded dialogue injection,
- local reasoning instructions,
- human-review requirements,
- explicit override rules,
- branch destinations.

The point of the 1–3 gate bound is to keep each local unit inspectable and composable.

## 5. Result semantics

### PASS
The gate's acceptance conditions are satisfied.

### FAIL
The gate's acceptance conditions are not satisfied.

### INTERMEDIATE
The current state is insufficient for a final binary result or the gate deliberately requires another bounded decision step.

`INTERMEDIATE` is an explicit result, not a silent fallback.

## 6. Audit path

A runtime trace should preserve enough linkage to reconstruct:

```text
.gst input state
      ↓
file_id
      ↓
gate_id
      ↓
trace_id
      ↓
result
      ↓
selected action/branch
      ↓
.qson event record
```

## 7. Determinism

G8SON contributes a deterministic control layer only when the runtime/profile fully defines:

- state-path resolution,
- condition semantics,
- operator semantics,
- missing-value handling,
- ordering,
- result mapping,
- branch behavior,
- override behavior,
- treatment of stochastic model/tool calls.

A probabilistic evaluator inside an atomic gate does not become deterministic merely because its output is wrapped in G8SON.
