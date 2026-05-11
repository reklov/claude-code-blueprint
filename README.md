# claude-code-blueprint

A template for starting new Claude-Code-driven components at
Schwarz Digits. Every new component begins by copying `core/`
out of this repo, choosing a language-pack, and following
`core/BOOTSTRAP.md`. The result is a repository with a
spec-conformant skeleton, seeded ADR-001 + Step-001, and the
build / test / smoke gates already in place.

## 30-second quickstart

```bash
# 1. Clone the blueprint as your new component's seed.
git clone <private-remote>/claude-code-blueprint <your-component>
cd <your-component>

# 2. Drop the blueprint's git history; the new repo starts fresh.
rm -rf .git

# 3. Promote core/ to the repo root.
cp -R core/. .
rm -rf core language-packs OPEN-QUESTIONS.md BLUEPRINT-SPEC.md README.md

# 4. Copy your chosen language-pack into the new repo.
#    (Adjust the path to wherever you cloned the blueprint; the
#     command assumes a sibling directory.)
cp ../claude-code-blueprint/language-packs/<language>.md \
   docs/_language-pack-<language>.md

# 5. Open core/BOOTSTRAP.md (now at the repo root: BOOTSTRAP.md)
#    and follow it from §0. It walks you through replacing
#    {{...}} placeholders, renaming the seed files, and making
#    the bootstrap commit.
$EDITOR BOOTSTRAP.md
```

Total wall-clock from step 1 to the bootstrap commit: ~15 minutes
for a single-language component, plus whatever time you spend
deciding the genesis ADR and the title of Step 001.

## What you'll have after the bootstrap

- A spec-conformant skeleton (CLAUDE.md, PLAN.md, README.md,
  FINDINGS.md, docs/HANDOFF.md, docs/architecture.md,
  docs/threat-model.md, docs/api.md, docs/plan/, docs/adrs/,
  docs/findings/, reference/, `.claude/settings.json`).
- The chosen language-pack bound into CLAUDE.md §"Build, test,
  smoke" — `pre-commit smoke`, `pre-merge gate`, `lint`, and
  release-build commands resolve to concrete shell commands.
- ADR-001 (the genesis decision) and Step 001 seeded; you write
  the first TDD list and the first failing test next.

## Where things live

| Question                            | File                              |
|-------------------------------------|-----------------------------------|
| How do I build / test / smoke?      | `docs/_language-pack-<lang>.md`   |
| What's already been decided?        | `docs/adrs/`                      |
| What's the current state?           | `PLAN.md`                         |
| What's the next concrete action?    | `docs/HANDOFF.md`                 |
| What are the hard rules?            | `CLAUDE.md`                       |
| How is the component structured?    | `docs/architecture.md`            |
| What's the public API?              | `docs/api.md`                     |
| What surprises have we hit?         | `FINDINGS.md` + `docs/findings/`  |
| Why does this blueprint look as it does? | `BLUEPRINT-SPEC.md` (this repo) |

## Feedback / contributing back

This blueprint is **v1.0** — derived from the conventions
proven out in the Sodium project, but not yet exercised against a
second component. The first teammates to use it are also the
first validators.

If something stumbles — an unclear instruction in
`core/BOOTSTRAP.md`, a placeholder that should not be there, a
missing convention you wish were enforced — please open an
issue or PR against this repo. Phase B of the blueprint plan is
explicitly waiting on real-use feedback.

Structural changes (new mandatory section, new language-pack,
new hard rule) start with `BLUEPRINT-SPEC.md` — the spec is the
contract; `core/` is its rendering. Conventions changes can land
in `core/CLAUDE.md` and propagate via the spec on the next
revision.

Existing components are not retroactively migrated when the
blueprint moves. They pull updates in deliberately, on their own
schedule.

## Repo contents

- [`BLUEPRINT-SPEC.md`](BLUEPRINT-SPEC.md) — the contract.
- [`core/`](core/) — skeleton for every mandatory file in a
  component repo, including
  [`core/BOOTSTRAP.md`](core/BOOTSTRAP.md) (transient, deleted
  after instantiation).
- [`language-packs/`](language-packs/) — populated packs
  ([`rust.md`](language-packs/rust.md),
  [`go.md`](language-packs/go.md)) plus the
  [`_template.md`](language-packs/_template.md) for future
  TypeScript / Kotlin packs.
- [`OPEN-QUESTIONS.md`](OPEN-QUESTIONS.md) — known gaps and
  unresolved spec questions, plus a section for collecting
  first-use feedback.

## Status

- **Version:** v1.0.0 — first handoff cut.
- **License:** TBD. This blueprint is currently a private
  template for Schwarz Digits internal use. License decision is
  deferred until v2, after first-component feedback. The
  `{{LICENSE}}` mustache that components fill in at bootstrap is
  independent of this — each instantiated component chooses its
  own license.
- **Provenance:** derived from the Sodium project's lived
  conventions (see `BLUEPRINT-SPEC.md` §9 / §11).
