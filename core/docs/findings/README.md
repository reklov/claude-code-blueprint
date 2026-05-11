<!--
  Skeleton for: <component-root>/docs/findings/README.md
  Governed by:  BLUEPRINT-SPEC.md §4.6
  Purpose:      Explains the index-plus-full-text model used for
                findings and how it relates to FINDINGS.md at the
                repo root.
-->

# {{COMPONENT-NAME}} — Findings (full text)

This directory holds one file per finding. The **index** lives at
[`../../FINDINGS.md`](../../FINDINGS.md) at the repo root; the
**full content** of each finding lives here as `<NNN>-<slug>.md`.

The split keeps the index scannable while letting individual
findings grow as much as they need to. After 100 findings a
single append-only file is unreadable; this layout still works at
that scale and beyond.

## Format

Use [`_template.md`](_template.md) as the starting point for any
new finding. Mandatory sections: Observation, Investigation,
Explanation, Implication, Workaround / Fix, References.

## Numbering

Three-digit, immutable. Even obsolete or superseded findings
keep their numbers. The same number that appears in the FINDINGS.md
index row is the file's name (`<NNN>-<slug>.md`).

## Status values

- `open` — discovered, no explanation or workaround yet.
- `understood` — explained, with workaround or fix recorded.
- `superseded by §NNN` — replaced by a later finding.
- `obsolete` — the condition no longer applies.

## Relationship to ADRs

Findings are observations, not architectural decisions. When a
finding leads to a decision (e.g. a workaround with strategic
weight), write an ADR that references the finding. The finding
remains the observation anchor; the ADR carries the decision
trail.
