<!--
  ADR-001 seed — created at bootstrap from _template.md.
  Governed by: BLUEPRINT-SPEC.md §4.4 and §7.

  ADR-001 is the "Genesis" ADR. It documents the initial
  architecture choice of the component: which language, which
  primary dependencies, which over-arching architectural style.
  The bootstrap process always creates this file; the component
  cannot exist without one.

  At bootstrap time the file's name is renamed from
  `001-{{genesis-decision-slug}}.md` to `001-<actual-slug>.md`.

  Placeholder notation:
    - {{GENESIS-DECISION-TITLE}}, {{OWNER}}, {{LANGUAGE-PACK}},
      {{language-pack}}, {{first-step-slug}} — bootstrap-replaced.
    - <…> — pattern, filled by the author.
-->

# ADR-001: {{GENESIS-DECISION-TITLE}}

**Status:** Proposed
**Date:** YYYY-MM-DD
**Deciders:** {{OWNER}}

---

## Context

<!--
  Why does this component exist? What problem does it solve?
  What constraints (organizational, technical, legal) shape the
  initial architecture? Keep it concrete; this is the document a
  new contributor reads to understand why the component looks
  the way it does.
-->
<why-this-component-exists>

<initial-constraints>

## Options considered

### Option A: <primary-option>

<description>

- **Pro:** <pro-1>
- **Con:** <con-1>

### Option B: <alternative-considered>

<description>

- **Pro:** <pro-1>
- **Con:** <con-1>

## Decision

**We <decision-in-imperative>.**

In particular:

- **Language:** {{LANGUAGE-PACK}}.
- **Primary dependencies:** <key-deps>.
- **Architectural style:** <style>.

<rationale>

## Consequences

### Positive

- <positive-consequence-1>

### Negative

- <negative-consequence-1>

### Neutral

- <neutral-consequence-1>

## Implementation Notes

- Step 001 ([`docs/plan/001-{{first-step-slug}}.md`](../plan/001-{{first-step-slug}}.md))
  begins the implementation against this decision.
- Language-pack reference:
  [`docs/_language-pack-{{language-pack}}.md`](../_language-pack-{{language-pack}}.md).

## References

- [`BLUEPRINT-SPEC.md`](../../BLUEPRINT-SPEC.md) — the blueprint this
  component instantiates.

## Revision History

| Version | Date       | Change                          |
|---------|------------|---------------------------------|
| 1.0     | YYYY-MM-DD | Initial ADR (genesis decision). |
