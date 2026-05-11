<!--
  Skeleton for: <component-root>/docs/HANDOFF.md
  Governed by:  BLUEPRINT-SPEC.md §4.3
  Purpose:      Session hand-off. The first file a new Claude
                session (or a returning human) reads. Answers
                "what state are we in and what do I do now?"
  Hard cap:     150 lines at runtime. The cap forces complete
                rewrites, and complete rewrites force currency.
                If you cannot fit, content is in the wrong file
                (typically: §1 is too long → some of it is step
                detail and belongs in docs/plan/NNN-*.md).
  Update model: Rewrite, not append. At every session end.
-->

# Handoff — {{COMPONENT-NAME}}

**Date:** YYYY-MM-DD
**Branch:** <current-branch>

<!--
  ## §0 First action
  The commands the resumer runs first, plus the expected output
  shape (test count, "ok" indicator, etc.). Concrete, not prose.
  Max ~30 lines.
-->
## §0 First action

```
git status                 # expect: clean tree on <current-branch>
<pre-commit-smoke-cmd>   # expect: <NNN> tests green, no warnings
```

<!--
  ## §1 Brief from previous session
  What was last merged, what happened since. NOT *why*. Why goes
  in ADRs and step files. Max ~25 lines.
-->
## §1 Brief from previous session

- Last merged: <step-or-substep-id> (<merge-sha>).
- Live-validated: <date-or-n/a>.
- Tests: <NNN> passing across the workspace.

<!--
  ## §2 Concrete first task
  What is next. Pointer to docs/plan/NNN-*.md for detail. Max
  ~40 lines.
-->
## §2 Concrete first task

- Open [`docs/plan/<next-step-slug>.md`](plan/<next-step-slug>.md).
- Per CLAUDE.md "Hard rules": write the failing tests first,
  show them, get approval, then implement.
- Branch to create: `step/<NNN>-<slug>`.

<!--
  ## §3 Quick reference
  Branch, current test count, last finding number, next action.
  Max ~15 lines.
-->
## §3 Quick reference

- Branch: `<current-branch>`
- Tests: <NNN> passing
- Last finding: §<NNN>
- Open question for the user: <question-or-none>

<!--
  ## §4 Update contract
  Verbatim. Do not edit at component-instantiation time — the
  contract is the same for every component.
-->
## §4 Update contract

Rewrite this file completely at every session end. Do not append.
If it grows past 150 lines, split or delete sections — do not
let it become a changelog. If you don't know what the previous
session did, read `git log` instead of guessing.
