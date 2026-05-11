<!--
  ADR template — copy to <NNN>-<slug>.md for a new decision.
  Governed by: BLUEPRINT-SPEC.md §4.4

  Pick the next free three-digit number. Numbering is immutable —
  superseded ADRs keep their numbers. The title is imperative
  ("Use X for Y", "Replace A with B"); status starts at Proposed
  and only ever moves to Accepted, Superseded by ADR-<NNN>, or
  Deprecated.

  Placeholder notation in this template:
    - <…>  pattern (filled per ADR by the author)
    - {{…}} would be bootstrap-replaced; this template has none,
            because per-ADR values are not bootstrap-replaceable.
-->

# ADR-<NNN>: <decision title in imperative form>

**Status:** <Proposed | Accepted | Superseded by ADR-<NNN> | Deprecated>
**Date:** YYYY-MM-DD
**Deciders:** <name(s)>

---

## Context

<!-- What is the situation, what is the problem, what constraints
     apply. State the facts; the rationale for one option goes in
     "Decision". -->
<context-paragraphs>

## Options considered

### Option A: <name>

<one-paragraph-description>

- **Pro:** <pro-1>
- **Pro:** <pro-2>
- **Con:** <con-1>
- **Con:** <con-2>

### Option B: <name>

<one-paragraph-description>

- **Pro:** <pro-1>
- **Con:** <con-1>

<!-- Add Option C, D, etc. as needed. Even rejected-out-of-hand
     options are worth recording in one paragraph so the next
     reader doesn't have to re-discover why they were rejected. -->

## Decision

**We <decision-in-imperative>.**

<rationale-paragraphs>

## Consequences

### Positive

- <consequence-1>

### Negative

- <consequence-1>

### Neutral

- <consequence-1>

## Implementation Notes

<!-- Concrete pointers for the implementer: which files / modules
     are affected, which Step(s) in PLAN.md carry the work, any
     migration sequencing. -->
<implementation-notes>

## References

- <adr-reference-or-external-doc>

## Revision History

| Version | Date       | Change                |
|---------|------------|-----------------------|
| 1.0     | YYYY-MM-DD | Initial ADR.          |
