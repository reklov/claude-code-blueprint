<!--
  Skeleton for: <blueprint-repo>/language-packs/README.md
  Purpose:      Explains what a language-pack is, how packs are
                added, and how they get into a component repo.
-->

# Language packs

A **language-pack** is a markdown document that turns the
language-neutral vocabulary of the core blueprint
(`pre-commit smoke`, `pre-merge gate`, `lint`, `release-build`,
…) into concrete commands for one programming language and its
toolchain.

The interface a language-pack must satisfy is defined in
[`../BLUEPRINT-SPEC.md`](../BLUEPRINT-SPEC.md) §5. Every pack
has the same set of mandatory sections; missing a section makes
the pack incomplete.

## Available packs

- [`rust.md`](rust.md) — Rust 2024-edition pack. Real, populated
  pack derived from the Sodium project's lived practice.
- [`go.md`](go.md) — Go 1.22+ pack. Populated from Go community
  conventions (Effective Go, golangci-lint, goreleaser); will
  be updated in place once a first-party Go component ships.
- [`_template.md`](_template.md) — empty skeleton with all
  required sections. Copy it to start a new pack.

Future packs (TypeScript, Kotlin / Kotlin Multiplatform) start
from `_template.md`.

## How packs get into a component

At bootstrap time (see [`../BOOTSTRAP.md`](../BOOTSTRAP.md))
exactly one pack (or, for polyglot components per spec §6.3, one
primary plus N secondaries) is copied into the component's repo
as `docs/_language-pack-{{language-pack}}.md`. The component's
`CLAUDE.md` then references that file under the "Build, test,
smoke — language-pack reference" section.

After bootstrap the pack is **owned by the component**. Updates
to the master pack in this repo do not flow downstream
automatically; they are pulled in deliberately. A component may
pin its pack at a particular blueprint version.

## Contributing a pack

1. Copy [`_template.md`](_template.md) to `<language>.md`.
2. Fill every required §5 section. Use real, lived practice; do
   not invent commands or conventions speculatively. Mark
   genuinely-not-yet-known content with
   `<!-- TBD: not yet practiced anywhere -->` and list those
   items in the blueprint's `OPEN-QUESTIONS.md`.
3. Add an entry to the "Available packs" list above.
