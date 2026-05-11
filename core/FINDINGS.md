<!--
  Skeleton for: <component-root>/FINDINGS.md
  Governed by:  BLUEPRINT-SPEC.md §4.6
  Purpose:      Index of live-discovered behaviour, gotchas, and
                surprises. Index only — full content per finding
                lives under docs/findings/<NNN>-<slug>.md.
  Update model: Append-only — one new row per finding. Status
                column is editable (open → understood → superseded
                / obsolete). Numbering is immutable.
-->

# Findings — {{COMPONENT-NAME}}

Index of live-discovered behaviour, gotchas, and surprises.
Full content per finding under [`docs/findings/`](docs/findings/).
Format details in [`docs/findings/README.md`](docs/findings/README.md).

<!--
  Status values:
    open               — discovered, no explanation or workaround yet
    understood         — explained, with workaround or fix recorded
    superseded by §NNN — replaced by a later finding
    obsolete           — condition no longer applies
  Two example rows are kept below to make the format unambiguous.
  Replace them with real findings as they accrue; do not delete
  the column header row.
-->

| Nr  | Title                          | Date       | Status     | Tags                  | Link                                  |
|-----|--------------------------------|------------|------------|-----------------------|---------------------------------------|
| 001 | <example-finding-title>        | YYYY-MM-DD | understood | smoke,external-api    | [→](docs/findings/001-<slug>.md)      |
| 002 | <another-finding-title>        | YYYY-MM-DD | open       | race-condition        | [→](docs/findings/002-<slug>.md)      |

<!--
  ## Update protocol
  Three bullets. Concise. The full update contract is in
  BLUEPRINT-SPEC.md §4.6 and docs/findings/README.md.
-->
## Update protocol

- One new row per finding, appended at the bottom of the table.
- Status changes are edited in place; the corresponding full-text
  file gets a Revision History line.
- Numbering is immutable. Obsolete and superseded findings keep
  their numbers.
