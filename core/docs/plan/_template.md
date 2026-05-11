<!--
  Step-file template — copy to <NNN>-<slug>.md for a new step.
  Governed by: BLUEPRINT-SPEC.md §4.5

  Section order is mandated. Two non-negotiables:
    - "TDD list — MANDATORY" must be filled BEFORE the first
      implementation commit on the step's branch.
    - The TDD list is not retroactively edited to match what was
      implemented. New tests discovered during implementation are
      checked in red, then made green — same rule.

  Placeholder notation in this template:
    - <…>  pattern (you fill it in fresh for each step)
    - {{…}} would be bootstrap-replaced; this template has none,
            because per-step values are not bootstrap-replaceable.
-->

# Step <NNN> — <Title>

**Status:** <merged | in progress | pending | blocked>
**Linked ADRs:** ADR-<NNN>, …
**Branch:** <branch-name> *(while in progress)*

<!--
  ## Historical names
  Add an entry whenever the step is renumbered. Entries append;
  prior bullets are not edited. The "current" line gets a
  closed date range when superseded.
-->
> **Historical names**
> - "Step <NNN>" — current. (No renames yet.)

## Goal

<!-- One paragraph: what this step achieves. Pointer to PLAN.md
     for status; pointer to architecture.md / api.md for context. -->
<one-paragraph-goal>

## Sub-steps

<!-- Optional. List sub-steps if the step is decomposed; otherwise
     write "(none — single-cycle step)". Each sub-step gets its
     own branch and merge per CLAUDE.md "Conventions". -->
- <NNN>a: <sub-step-summary>
- <NNN>b: <sub-step-summary>

## Public API surface

<!-- New public functions / types / endpoints / commands this step
     introduces. Signatures, not prose. Cross-reference docs/api.md
     for the human-readable form. -->
```
<signatures-or-endpoint-list>
```

## TDD list — MANDATORY, written before any implementation

> **Workflow reminder.** Per `CLAUDE.md` hard rules: this list
> is filled BEFORE the step branch is created. The first commit
> on the branch is the failing tests themselves — red, then
> reviewed with the user, then implementation goes green one
> test at a time. Per Tidy-First, tidyings live in their own
> commits separate from the behaviour-changing commits.

<!--
  Ordered checklist of failing tests that must exist before the
  first implementation commit on this branch. Each test is checked
  off when it goes green. The order in this list is the order of
  the implementation commits.

  If you discover during implementation that an additional test
  is needed, check it in red first (its own commit), then make
  it green. Do not retro-fit the list to match what was already
  implemented.
-->
- [ ] <test-1-description> — *(red → green → refactor)*
- [ ] <test-2-description>
- [ ] <test-3-description>

## Smoke test

<!-- Concrete command(s) that exercise the step end-to-end. Names
     `pre-merge gate` (defined by the language-pack) plus any
     step-specific live invocation. -->
```
<smoke-command>
```

Expected outcome: <one-line-expectation>.

## Out of scope for this step

<!-- What is intentionally not part of this step, with a pointer
     to the later step that will cover it. State things you have
     considered and explicitly pushed out; this keeps reviewers
     from second-guessing the scope. -->
- Error retry / backoff logic — deferred to Step <later-NNN>.
- <item> — deferred to Step <later-NNN>.

## Files touched

<!-- Table of files this step creates or modifies. Helps reviewers
     orient quickly and forces the author to think about the
     blast radius. Production code and test code in the same
     row pair (the test row goes first per TDD); commit-time
     this matches the order of commits on the branch. -->
| File | Action | Reason |
|---|---|---|
| `src/<module>/<feature>.rs` | new | implementation of <feature> |
| `src/<module>/<feature>_test.rs` (or inline `#[cfg(test)]`) | new | TDD tests from the list above |
| `<path/to/other.ext>` | edit | <reason> |
