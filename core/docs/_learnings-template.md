<!--
  LEARNINGS template — copy to docs/LEARNINGS.md when you reach
  a retrospective moment (end of an implementation pass, a major
  rewrite boundary, after a production incident with broad
  lessons). Optional file per BLUEPRINT-SPEC.md §4.7.

  Purpose: capture what was learned in a form that the next pass
  (or the next contributor) reads first. Distinct from FINDINGS:
  findings are per-incident observations; learnings are the
  synthesised conclusions across many findings.

  Format conventions:
    - One H2 section per theme (Assumptions that broke, Tools
      that did not pay off, Architectural moves to do earlier
      next time, Tools that did pay off, etc.).
    - Each bullet states: what was assumed → what was observed →
      what the next pass should do differently.
    - English (per CLAUDE.md hard rule).
    - Cite FINDINGS / ADR / commit-SHAs where they back the
      claim. Future-you will not remember the evidence.

  Placeholder notation in this template:
    - <…> pattern (you fill it in fresh for each retrospective)
    - {{…}} would be bootstrap-replaced; this template has none.
-->

# Learnings — <component-name>

**Pass / period:** <e.g. "v1 first-pass implementation, 2026-01 through 2026-04">
**Author(s):** <name(s)>
**Date written:** YYYY-MM-DD

## Assumptions that broke

<!-- For each: what we assumed, what we observed, what the next
     pass should do from day one. -->
- **Assumed:** <prior belief>.
  **Observed:** <what actually happened, with reference to FINDING §<NNN> or commit <sha>>.
  **Next time:** <concrete action>.

## Architectural moves to do earlier

<!-- Decisions we made late that, in hindsight, should have been
     made at component genesis. Cross-reference the ADR that
     eventually documented the decision. -->
- <move>: <one-paragraph rationale>. ADR-<NNN> documents the
  current state; ideal placement would have been at component
  genesis (ADR-001 territory).

## Tools / techniques that paid off

<!-- The ones to keep, recommend onward, write into language-pack
     defaults if generic. -->
- <tool or technique>: <why it worked, with reference>.

## Tools / techniques that did not pay off

<!-- Things we tried and abandoned. Saves the next contributor
     from reinventing the same wrong wheel. -->
- <tool or technique>: <why we dropped it, with reference>.

## Open patterns worth promoting to the blueprint

<!-- If a pattern emerged in this component that would benefit
     every future Claude-Code-driven component, name it here and
     open an issue or PR against the blueprint repo. -->
- <pattern>: <one-line description>. Filed as <issue / PR link>.

## References

- FINDING §<NNN>, §<MMM>, …
- ADR-<NNN>, …
- Cross-cutting commits: <sha>, …
- External post-mortems / incident reports.

## Revision History

| Date       | Change                                          |
|------------|-------------------------------------------------|
| YYYY-MM-DD | Created at <retrospective trigger>.             |
