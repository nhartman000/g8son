# G8SON — Gate Sequential Object Notation

**G8SON** is Nicholas Hartman / American Milestone Inc.'s deterministic conditional-gate notation for bounded, auditable execution inside the MG8 file family.

A `.g8son` file defines **1–3 gates maximum**. Gates evaluate structured state and contextual constraints supplied by `.gst`, determine whether a transform or continuation is admissible, and return a bounded status that orchestration can act upon.

## Role in MG8

```text
.gst structured state/context
        ↓
.g8son conditional gate/operator
        ↓
PASS / FAIL / INTERMEDIATE
        ↓
.ork sequencing / branch decision
        ↓
.qson execution trace
```

G8SON is not the state store, orchestration file, or audit ledger:

- `.gst` carries structured state and constraints.
- `.g8son` defines bounded conditional gates/operators.
- `.ork` determines execution order, branches, retries, and flow.
- `.qson` records realized execution events.
- `.mg8` binds these artifacts into a bounded execution unit.

## Identity model

G8SON distinguishes three identities:

```text
file_id != gate_id != trace_id
```

- `file_id` identifies the `.g8son` document.
- `gate_id` identifies a gate definition inside the file.
- `trace_id` identifies one attempted execution of one gate.

Repeated execution of the same gate therefore produces distinct trace events.

## Gate count

A canonical `.g8son` file contains **one to three gates**. This keeps each file bounded and locally auditable rather than turning one file into an unbounded reasoning graph.

## Gate results

The canonical result space is:

- `PASS` — the gate's required conditions are satisfied.
- `FAIL` — the gate's required conditions are not satisfied.
- `INTERMEDIATE` — the gate cannot resolve to PASS/FAIL without additional state, a bounded local reasoning step, an override, human input, or another declared continuation.

`INTERMEDIATE` is not implicit permission to continue. The `.ork` flow or gate policy must explicitly define what happens next.

## Gate content

A gate may include:

- ordered requirements or conditions,
- references into `.gst` state,
- deterministic logic operators,
- precedent/context references,
- bounded dialogue or reasoning injection,
- explicit overrides,
- branch destinations,
- human-decision requirements,
- local metadata needed for traceability.

These richer fields are optional and profile-dependent; they do not change the one-to-three-gate file bound.

## Minimal example

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

The example is intentionally generic. Domain behavior belongs in authored gate/state files rather than in the G8SON standard itself.

## Audit relationship

Every attempted gate execution should produce a `.qson` event with its own `trace_id`, linked back to the relevant `file_id`, `gate_id`, input-state reference, result, and execution context.

## Repository structure

- [`spec/g8son_v1.md`](spec/g8son_v1.md) — canonical G8SON v1 specification
- [`schema/g8son.schema.json`](schema/g8son.schema.json) — bounded file-schema contract
- [`examples/basic.g8son`](examples/basic.g8son) — minimal one-gate example
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — G8SON execution and identity architecture
- [`docs/PROVENANCE.md`](docs/PROVENANCE.md) — provenance and scope notes

## Status

This repository defines the generic G8SON standard. Domain-specific policy, legal logic, defense logic, compliance logic, model prompts, or application-specific reasoning should be authored in separate `.g8son`/`.gst` content rather than hard-coded into the file-format standard.
