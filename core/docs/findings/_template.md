<!--
  Finding template — copy to <NNN>-<slug>.md for a new finding.
  Governed by: BLUEPRINT-SPEC.md §4.6

  Pick the next free three-digit number. Append a row to
  FINDINGS.md (the index) in the same commit. Numbering is
  immutable; status moves over time.

  Placeholder notation in this template:
    - <…>  pattern (filled per finding by the author)
    - {{…}} would be bootstrap-replaced; this template has none,
            because per-finding values are not bootstrap-replaceable.
-->

# Finding §<NNN> — <title>

**Date:** YYYY-MM-DD
**Status:** <open | understood | superseded by §<NNN> | obsolete>
**Tags:** <comma-separated, e.g. smoke, external-api, race-condition>
**Discovered during:** <Step <NNN> smoke / Live debugging / Code review / …>

## Observation

<!-- What exactly was observed. Include concrete artifacts: command
     output, log excerpts, stack traces, request/response bodies.
     Future-you should be able to reconstruct the surprise without
     having to rerun the scenario. -->
<observation-with-evidence>

## Investigation

<!-- What was done to understand the observation. Tools used,
     hypotheses tried and discarded, sources consulted. -->
<investigation-narrative>

## Explanation

<!-- Why this happens. The cause, not just the symptom. -->
<explanation>

## Implication

<!-- What this means for the implementation: a concrete consequence
     for code, tests, ops, or future work. -->
<implication>

## Workaround / Fix

<!-- How we deal with it: a workaround in code, an ADR-level
     decision, or a deferred follow-up. Cite the commit / ADR /
     step that carries the action. -->
<workaround-or-fix>

## References

- <commit-sha-or-pr-link>
- <adr-reference-if-any>
- <step-reference-if-any>
- <external-issue-or-spec-if-any>

## Revision History

| Date       | Change                                        |
|------------|-----------------------------------------------|
| YYYY-MM-DD | Created.                                      |
