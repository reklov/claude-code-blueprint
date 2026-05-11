<!--
  Skeleton for: <component-root>/PLAN.md
  Governed by:  BLUEPRINT-SPEC.md §4.2
  Purpose:      Status truth — what is merged, in progress, pending,
                or blocked. Only status. No prose, no decisions.
  Hard cap:     240 lines at runtime. If you cross it, the content
                is in the wrong place — move detail to docs/plan/,
                decisions to docs/adrs/, conventions to CLAUDE.md.
                (This skeleton fits well under the cap.)
-->

# {{COMPONENT-NAME}} Implementation Plan

<!--
  Header paragraph (3–5 sentences) stating that this file is *only*
  status. Cross-link to CLAUDE.md (conventions), docs/plan/ (per-
  step prose), docs/adrs/ (decisions). The point is to make it
  impossible to mistake PLAN.md for a place to write rationale.
-->
This file is the canonical roadmap **status** — what is merged, in
progress, pending, or blocked. The per-step **prose** (sub-steps,
public API, TDD lists, implementation notes) lives under
[`docs/plan/`](docs/plan/) — one file per step. The **conventions**
live in [`CLAUDE.md`](CLAUDE.md). **Architectural decisions** are
recorded as ADRs under [`docs/adrs/`](docs/adrs/).

<!--
  ## Status overview
  The single table is the only truth about step status for this
  component. Update it in the same commit as the status change it
  reflects. Status values: merged, in progress, pending, blocked.
-->
## Status overview

Last update: **YYYY-MM-DD**.

| Step | Title | Status | Branch / PR | Notes |
|---|---|---|---|---|
| [001](docs/plan/001-{{first-step-slug}}.md) | {{FIRST-STEP-TITLE}} | pending | — | — |

<!--
  ### Cross-cutting commits on `main` (not part of any single step)
  Bullets for commits that don't belong to a single step: tooling
  changes, doc refactors, dependency bumps, etc. Keep one line per
  commit; each line starts with the short SHA.
-->
### Cross-cutting commits on `main`

- _(none yet — list `<short-sha> <one-line summary>` per commit)_

<!--
  ### How to resume
  Numbered steps for someone resuming after a break. Typical:
  read HANDOFF, check branch, run pre-commit smoke, open the
  current step file. Concrete commands; no prose.
-->
### How to resume

1. Read [`docs/HANDOFF.md`](docs/HANDOFF.md) for current state.
2. `git status` — verify clean tree on the expected branch.
3. Run `pre-commit smoke` (see CLAUDE.md §"Build, test, smoke").
4. Open the topmost **in progress** step file linked above.
5. Per CLAUDE.md TDD rule: write failing tests first, get them
   reviewed, only then implement.

<!--
  ### Update protocol for this table
  Three bullets, no more. Tells when to update, who updates, and
  what kinds of edits are out of place here.
-->
### Update protocol for this table

- The first commit on a new step / sub-step branch flips the row
  to **in progress** and writes the branch name into the
  Reference column.
- The merge commit to `main` flips the row to **merged** and adds
  the merge-commit SHA (and live-validation date if applicable).
- Out-of-date status rows are a review defect, same class as
  out-of-date `docs/api.md` (CLAUDE.md "Documentation freshness").
