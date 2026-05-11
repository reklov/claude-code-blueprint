# claude-code-blueprint

Bootstrap template for new Claude-Code-driven components at
Schwarz Digits. Clone, start Claude, answer a few questions —
Claude handles the rest and produces a working component
skeleton.

## Quickstart

```bash
git clone <private-remote>/claude-code-blueprint <your-component>
cd <your-component>
claude
```

Claude reads [`CLAUDE.md`](CLAUDE.md) on start and walks you
through the bootstrap interactively. You answer six questions
(component name, owner, language pack, license, genesis ADR
title, first-step title), confirm the plan Claude presents, and
Claude executes everything: placeholder substitution, file
renames, language-pack copy, deleting the blueprint-only files,
fresh `git init`, first commit.

When the bootstrap is done Claude asks you to **restart Claude
Code** so the new component's permanent CLAUDE.md takes effect.
Your first real task is documented in `docs/HANDOFF.md`.

If Claude doesn't pick up the bootstrap automatically when you
start it, prompt it explicitly:

> Read CLAUDE.md and start the bootstrap.

## Failure recovery

If anything goes wrong mid-bootstrap, nuke the partial clone and
start over:

```bash
cd ..
rm -rf <your-component>
git clone <private-remote>/claude-code-blueprint <your-component>
cd <your-component>
claude
```

The blueprint repo is unaffected; only the partial clone is
discarded.

## What this repo contains

- [`CLAUDE.md`](CLAUDE.md) — bootstrap-mode instructions for
  Claude. Deleted after a successful bootstrap.
- [`BLUEPRINT-SPEC.md`](BLUEPRINT-SPEC.md) — the contract. File
  hierarchy, section schema per file, update protocols,
  language-pack interface, hard rules. Stays in the blueprint
  repo; removed from the component repo at bootstrap.
- [`core/`](core/) — skeleton that becomes the component's repo
  root: `CLAUDE.md`, `PLAN.md`, `README.md`, `FINDINGS.md`,
  `docs/{HANDOFF, architecture, threat-model, api}.md`,
  `docs/plan/`, `docs/adrs/`, `docs/findings/`, `reference/`,
  `.claude/settings.json`, `.gitignore`. Promoted to the repo
  root at bootstrap.
- [`language-packs/`](language-packs/) — populated packs
  ([`rust.md`](language-packs/rust.md),
  [`go.md`](language-packs/go.md)) plus
  [`_template.md`](language-packs/_template.md) for future
  TypeScript / Kotlin packs. The chosen pack is copied into the
  component as `docs/_language-pack-<pack>.md` at bootstrap.
- [`OPEN-QUESTIONS.md`](OPEN-QUESTIONS.md) — blueprint-level gaps
  and first-use feedback collection. Stays in the blueprint repo.

## Feedback / contributing back

This blueprint is **v2** — it has not yet been exercised against
a real component. The first teammates to use it are also the
first validators.

If something stumbles during bootstrap — an unclear question, a
file that wasn't deleted but should have been, a placeholder
that survived — please open an issue or PR against this repo.
The Phase B work plan is explicitly waiting on real-use feedback.

Structural changes (new mandatory section, new language-pack,
new hard rule) start with `BLUEPRINT-SPEC.md` — the spec is the
contract; `core/` is its rendering. Conventions changes can
land in `core/CLAUDE.md` and propagate via the spec on the next
revision.

Existing components are not retroactively migrated when the
blueprint moves. They pull updates in deliberately, on their own
schedule.

## Status

- **Version:** v2.0.0 — Claude-Code-driven bootstrap.
  Previous tag `v1.0.0` used a manual checklist (`core/BOOTSTRAP.md`)
  which has been removed.
- **License:** TBD. This blueprint is currently a private
  template for Schwarz Digits internal use. License decision is
  deferred until first-component feedback informs the v3 call.
  The `{{LICENSE}}` mustache that components fill in at bootstrap
  is independent — each instantiated component chooses its own
  license.
- **Provenance:** derived from the Sodium project's lived
  conventions (see `BLUEPRINT-SPEC.md` §9 / §11).
