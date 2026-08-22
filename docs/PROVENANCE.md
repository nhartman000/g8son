# G8SON Provenance and Scope

**Author:** Nicholas Hartman / American Milestone Inc.

This document records the canonical baseline for **G8SON — Gate Sequential Object Notation** within the MG8 file family.

## Established baseline

The established G8SON architecture includes:

- `.g8son` as the gate file format,
- one to three gates per `.g8son` file,
- bounded conditional evaluation against `.gst` state/context,
- canonical gate results `PASS`, `FAIL`, and `INTERMEDIATE`,
- distinct file identity, gate identity, and runtime execution identity,
- a unique `trace_id` for every attempted gate execution,
- support for richer local gate content such as precedent, dialogue injection, overrides, branching, and bounded local reasoning,
- orchestration handled outside the file by `.ork`,
- realized execution/audit events written to `.qson`.

## Generic standard versus application content

G8SON is intended as a generic framework. Domain-specific behavior belongs in authored gate and state files rather than in the standard itself.

Examples of domain-specific content that should remain outside the base standard include:

- legal doctrine,
- regulatory policy,
- defense mission rules,
- organization-specific approval chains,
- application-specific prompts,
- application-specific confidence thresholds.

The standard defines how bounded gates are represented and traced; it does not prescribe the substantive policy loaded into them.

## Repository correction note

The repository previously contained a useful but unfinished AI-generated README draft with embedded meta-instructions such as “copy-paste directly,” plus a strong legal/compliance positioning that made one application domain appear to define the format itself.

The canonical baseline removes those drafting artifacts and separates the generic G8SON standard from example application domains.

## Relationship to the MG8 family

```text
.gst    structured state/context
.g8son  bounded conditional gates/operators
.ork    sequencing/orchestration
.qson   immutable/auditable execution trace
.mg8    bounded unit binding the artifacts
.mg8pk  package/composition layer
```

## Scope boundary

This repository defines G8SON. It does not fully specify the independent grammars for `.gst`, `.ork`, `.qson`, `.mg8`, or `.mg8pk`.
