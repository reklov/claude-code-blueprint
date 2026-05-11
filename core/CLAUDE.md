<!--
  Skeleton for: <component-root>/CLAUDE.md
  Governed by:  BLUEPRINT-SPEC.md §4.1
  Purpose:      Permanent conventions and hard rules. Anything that
                stays the same across sessions and is not day-to-day
                state.
  Caps:         No hard line cap — this is reference material, not
                reading flow. If a section grows, ask whether the
                content belongs in CLAUDE.md, an ADR, or the
                language-pack.
  Section order is mandated by spec §4.1. Do not reorder.
-->

# {{COMPONENT-NAME}} — Conventions for Claude Code

<!--
  ## Reading order
  Tells a new contributor (human or Claude) which files to read in
  which order to ramp on this component. Standard recommendation:
    docs/HANDOFF.md → CLAUDE.md → PLAN.md
      → docs/plan/<current-step-slug>.md
      → relevant ADRs.
-->
## Reading order

1. `docs/HANDOFF.md` — current session state.
2. `CLAUDE.md` (this file) — permanent conventions.
3. `PLAN.md` — status of all steps.
4. `docs/plan/<current-step-slug>.md` — what to work on.
5. Relevant ADRs under `docs/adrs/`.

<!--
  ## Hard rules
  Non-negotiables. Maximum 10 bullets. The first six are mandated
  by spec §4.1 and must appear verbatim in every component. Add
  component-specific hard rules below them, but stop at 10 — past
  that it is convention, not a hard rule.
-->
## Hard rules

- **Strict TDD always.** Tests are written before implementation
  and must be red before any production code is written. This is
  language- and tooling-independent. When working with Claude Code
  it is non-negotiable: the test list is the contract the AI
  implements against.
- **English for committed content.** Code, comments, commit
  messages, docs, ADRs, plan files, findings — all English.
- **Pre-commit smoke must pass before any commit.** The concrete
  command set is defined in the language-pack (see "Build, test,
  smoke" below).
- **Out-of-date documentation is a defect in review, not a
  follow-up task.**
- **Documentation update happens in the same commit as the code
  change that necessitates it.**
- **Tidyings stay separate from behavioural changes.** A commit
  either tidies (renames, extractions, formatting, dead-code
  removal — no behaviour change) or it changes behaviour — never
  both. Reviews stay sharp; reverts stay surgical.
  (Kent Beck, *Tidy First?*)
- **Accepted ADRs are not edited; supersession is the only forward
  path.**
- <component-specific-hard-rule-if-any>

<!--
  ## User preferences
  How the principal owner(s) of this component prefer to work with
  Claude Code: language for chat, tool preferences, response style.
  Anything that is true for this component but not the org default
  goes here. Org-default content can be linked rather than copied.
-->
## User preferences

- {{OWNER}} prefers <chat-language> for conversational chat;
  committed content remains English per Hard rules.
- <tool-preferences-if-any>.
- <response-style-if-any>.

<!--
  ## Architecture overview
  A short architecture recap (a few paragraphs and/or a diagram).
  States what the component is, where it sits, what it depends on
  externally. NOT a step plan (that is PLAN.md). NOT decision
  rationale (that is ADRs). The full architecture document lives at
  docs/architecture.md — this section is the elevator pitch.
-->
## Architecture overview

<one-paragraph-recap-of-what-this-component-is-and-where-it-sits>

See `docs/architecture.md` for the canonical architecture document.

<!--
  ## Functional scope
  In-scope vs explicitly out-of-scope. This is the canonical source
  for scope; PLAN.md links here, does not duplicate.
-->
## Functional scope

**In scope:**
- <in-scope-item-1>
- <in-scope-item-2>

**Explicitly out of scope:**
- <out-of-scope-item-1>
- <out-of-scope-item-2>

<!--
  ## Build, test, smoke — language-pack reference
  This section is the contract that binds the language-pack into
  the component. Point to the pack file and name the operations
  whose concrete commands the pack defines (pre-commit smoke,
  pre-merge gate, lint, format, release-build).

  For multi-language components (spec §6.3) replace this section
  with an aggregation table that composes per-sub-area pack
  commands into top-level operations.
-->
## Build, test, smoke — language-pack reference

This component uses the **{{LANGUAGE-PACK}} language-pack**.

See [`docs/_language-pack-{{language-pack}}.md`](docs/_language-pack-{{language-pack}}.md)
for the concrete commands behind:

- `pre-commit smoke`
- `pre-merge gate`
- `lint`, `format`, `format-check`
- `unit-tests`, `integration-tests`
- `dep-license-check`
- `api-docs-generate`
- `release-build`

<!--
  ## Conventions
  Component-specific conventions that are language-neutral and not
  in the language-pack. File naming, test naming, commit-message
  format, branch policy. Anything that is purely language-specific
  belongs in the language-pack, not here.
-->
## Conventions

- **Branch naming:** `step/<NNN>-<slug>` for full steps,
  `step/<NNN><letter>-<slug>` for sub-steps.
- **Commit prefix:** `feat(step-<NNN>): …`,
  `chore(step-<NNN>): …`, `docs(step-<NNN>): …`.
- **One commit per completed step or sub-step on its branch.**
  Small fix-up commits inside a branch are fine; rolling two steps
  into one merge commit is not.

<!--
  ## Documentation freshness
  The freshness rule. Every code change with user-visible effect
  updates the affected doc in the same commit. Out-of-date docs
  are review defects.
-->
## Documentation freshness

- A change to the public API updates `docs/api.md` in the same
  commit.
- A change to architecture updates `docs/architecture.md` in the
  same commit.
- A change to user-visible build or invocation updates `README.md`
  in the same commit.
- A change to security assumptions or trust boundaries updates
  `docs/threat-model.md` in the same commit.

Out-of-date documentation is treated as a defect in review, not a
follow-up task.

<!--
  ## Update policy
  What may change in CLAUDE.md, what may not, and how. Three to
  five bullets. Concise.
-->
## Update policy

- Changes to **Hard rules** or **Functional scope** require an ADR
  reference in the commit.
- Language-specific content does not enter CLAUDE.md — it goes
  into `docs/_language-pack-{{language-pack}}.md`.
- This file may grow as the component matures; it does not have a
  hard line cap. If a section becomes unwieldy, move detail into
  `docs/architecture.md`, `docs/api.md`, or an ADR and leave a
  pointer here.
