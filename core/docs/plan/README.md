<!--
  Skeleton for: <component-root>/docs/plan/README.md
  Governed by:  BLUEPRINT-SPEC.md §4.5
  Purpose:      Index + format explanation for the per-step files
                that live in this directory. Step *status* lives
                in PLAN.md at the repo root; this directory holds
                step *prose*.
-->

# {{COMPONENT-NAME}} — Per-step files

This directory holds one file per implementation step.

The canonical source of truth for **what is currently merged, in
progress, or pending** is the status table in
[`../../PLAN.md`](../../PLAN.md). The per-step files here carry
the prose: sub-steps, public API, TDD lists, implementation notes.

## Filename convention

`<NNN>-<short-slug>.md`, where `<NNN>` is a three-digit zero-padded
step number. Sub-step suffixes use lowercase letters
(`001a-…`, `001b-…`).

## Format

Use [`_template.md`](_template.md) as the starting point for any
new step file. The mandatory sections are listed there; in
particular, the **TDD list** must exist before the first
implementation commit on the step's branch.

## Renumbering

When a new step is inserted between existing ones, every later
step file is `git mv`'d to its new name and gains an entry in its
"Historical names" metadata block. Git history preserves the
rename; old branch names and commit messages referencing the
previous number remain traceable through that block.
