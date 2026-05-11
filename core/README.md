<!--
  Skeleton for: <component-root>/README.md
  Governed by:  BLUEPRINT-SPEC.md §4 (no dedicated section; spec
                §10 OQ-3 records that a light README template is
                expected). Keep this short and public-facing.
  Audience:     A first-time visitor who lands on the repo —
                contributor or consumer. Tells them what this is,
                how to build, where to read more.
  Length:       Aim for under ~150 lines. If it grows, move detail
                into docs/.
-->

# {{COMPONENT-TITLE}}

<!--
  One-paragraph "what is this". Keep it under five sentences. The
  detailed motivation belongs in CLAUDE.md and the genesis ADR.
-->
<one-paragraph-description-of-what-this-component-does>

Owner: {{OWNER}}.

## Documentation map

- [`CLAUDE.md`](CLAUDE.md) — contributor-facing conventions and
  hard rules.
- [`PLAN.md`](PLAN.md) — current roadmap status.
- [`docs/architecture.md`](docs/architecture.md) — component
  architecture.
- [`docs/api.md`](docs/api.md) — public API reference.
- [`docs/threat-model.md`](docs/threat-model.md) — security
  assumptions.
- [`docs/adrs/`](docs/adrs/) — architecture decision records.
- [`FINDINGS.md`](FINDINGS.md) — index of live-discovered behaviour
  and gotchas.

## Building

<!--
  Pre-requisites and the build command. Concrete commands come
  from the language-pack — point at the pack file and quote the
  most common ones inline if useful.
-->
Prerequisites and build commands are defined in the
{{LANGUAGE-PACK}} language-pack:
[`docs/_language-pack-{{language-pack}}.md`](docs/_language-pack-{{language-pack}}.md).

The mandatory pre-commit gate (`pre-commit smoke`) and the
pre-merge gate are defined there. Both must pass before any
commit lands on `main`.

## Quick start

<!--
  Minimal working example. For a library: a "hello world" snippet.
  For a service: how to run it locally. For a CLI: the smallest
  useful invocation. Keep it to a handful of lines; full reference
  belongs in docs/api.md.
-->
<quick-start-command-or-snippet>

See [`docs/api.md`](docs/api.md) for the full reference.

## License

{{LICENSE}} (see [`LICENSE`](LICENSE)).
