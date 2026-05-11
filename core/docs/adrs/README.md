<!--
  Skeleton for: <component-root>/docs/adrs/README.md
  Governed by:  BLUEPRINT-SPEC.md §4.4
  Purpose:      Index of all ADRs plus a concise format and
                lifecycle summary. Full ADR content lives in the
                per-file documents in this directory.
-->

# {{COMPONENT-NAME}} — Architecture Decision Records

Every decision someone will later question is recorded here, with
the alternatives that were considered and the rationale.

Use [`_template.md`](_template.md) as the starting point for any
new ADR. Numbering is three-digit (`001`, `002`, …) and immutable
— superseded ADRs keep their number.

## Index

| Nr  | Title                                  | Status     | Date       |
|-----|----------------------------------------|------------|------------|
| 001 | {{GENESIS-DECISION-TITLE}}             | Proposed   | YYYY-MM-DD |

## Status lifecycle

`Proposed` → `Accepted` → either `Superseded by ADR-NNN` (with a
pointer to the successor) or `Deprecated`.

**Accepted ADRs are not edited beyond typo / link repair.** When
the world changes, write a new ADR that supersedes the old one;
do not edit the original decision.

## Format

Each ADR contains: Status, Date, Deciders, Context, Options
considered (with Pro/Con), Decision (imperative), Consequences
(Positive / Negative / Neutral), Implementation Notes,
References, Revision History. The template carries the full
shape.
