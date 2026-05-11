<!--
  Language-pack template — copy to <language>.md to start a new pack.
  Governed by: BLUEPRINT-SPEC.md §5

  Every required section below MUST be present and populated for
  the pack to be considered complete. Genuinely-unknown content
  is marked with `<!-- TBD: ... -->` and listed in the blueprint
  repo's OPEN-QUESTIONS.md, not silently elided.

  Placeholder notation in this template:
    - <…>  pattern (filled per language by the pack author)
    - {{…}} would be bootstrap-replaced; this template has none,
            because per-pack values are not bootstrap-replaceable.
-->

# Language-Pack: <Language>

<!--
  ## Build manifest
  Which manifest file (Cargo.toml / go.mod / package.json /
  build.gradle.kts / pyproject.toml). Minimum toolchain version
  and edition / language standard. Workspace vs single-package
  layout.
-->
## Build manifest

<manifest-file-and-version-constraints>

<!--
  ## Module / package layout
  Where source files live, where tests live, where docs live.
  Use the language's idiomatic layout, not an invented one.
-->
## Module / package layout

<layout-diagram-or-list>

<!--
  ## Test position convention
  Where unit tests, integration tests, and doc-tests / examples
  live. Be explicit about colocation rules.
-->
## Test position convention

<test-positions>

<!--
  ## Language-neutral operations → concrete commands
  The contract that binds the core blueprint vocabulary to this
  toolchain. Every row must be filled; if a row genuinely doesn't
  exist for this language, write `n/a — <reason>` so the omission
  is visible.
-->
## Language-neutral operations → concrete commands

| Language-neutral operation | Concrete command |
|---|---|
| `pre-commit smoke`     | `<format-check> && <lint> && <unit-tests>` |
| `pre-merge gate`       | `pre-commit smoke && <integration-tests> && <coverage-check>` |
| `lint`                 | `<lint-command>` |
| `format`               | `<format-command>` |
| `format-check`         | `<format-check-command>` |
| `unit-tests`           | `<unit-test-command>` |
| `integration-tests`    | `<integration-test-command>` |
| `dep-license-check`    | `<license-check-command>` |
| `api-docs-generate`    | `<doc-gen-command>` |
| `release-build`        | `<release-build-command>` |

<!--
  ## Error-handling idiom
  Which error-propagation pattern is standard in this language
  (Result, error return, exception, sealed). Examples of correct
  usage in real code style.
-->
## Error-handling idiom

<idiom-and-examples>

<!--
  ## Common pitfalls
  Known foot-guns of the language and toolchain. Each pitfall is
  a one-line description plus the workaround / convention that
  avoids it.
-->
## Common pitfalls

- <pitfall-1>
- <pitfall-2>

<!--
  ## Recommended dependencies
  De-facto-standard libraries for typical concerns (logging, tests,
  serialization, HTTP, async). One line of rationale per choice.
-->
## Recommended dependencies

| Concern | Library | Rationale |
|---|---|---|
| logging       | <lib> | <why> |
| testing       | <lib> | <why> |
| serialization | <lib> | <why> |
| HTTP client   | <lib> | <why> |
| async runtime | <lib> | <why> |

<!--
  ## Release & versioning
  How SemVer is applied in this language, the tag convention,
  and what release artifacts the pack produces. If the language
  has a registry (crates.io, npm, Maven Central, PyPI, etc.),
  describe the publish flow here.
-->
## Release & versioning

<semver-rules-tag-convention-publish-flow>

<!--
  ## Cross-compilation targets (optional, recommended)
  If the language supports cross-compilation and the component
  may need it, list the relevant targets and the toolchain
  invocation.
-->
## Cross-compilation targets *(optional)*

<targets-or-n-a>

<!--
  ## IDE / editor setup (optional, recommended)
  Plug-ins, language servers, settings that make life easier.
-->
## IDE / editor setup *(optional)*

<ide-notes-or-n-a>
