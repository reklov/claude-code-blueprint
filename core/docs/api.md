<!--
  Skeleton for: <component-root>/docs/api.md
  Governed by:  BLUEPRINT-SPEC.md §4.7
  Purpose:      Public API reference. The shape depends on the
                component type:
                  - library  → function signatures, usage examples
                  - service  → endpoints, request/response schemas, auth
                  - CLI      → commands, flags, exit codes, env vars
                  - bindings → per-target-language consumption notes
                If the component exposes more than one of these,
                either keep them as sections in this file *or*
                split into docs/api-library.md / docs/api-cli.md /
                docs/api-bindings.md (spec §4.7) and let api.md
                index them.
  Update rule:  Same-commit updates per CLAUDE.md "Documentation
                freshness". A change to a public symbol or
                endpoint updates the corresponding section in
                the same commit.
-->

# {{COMPONENT-NAME}} — Public API

<!-- One-paragraph orientation: what kind of API surface this
     component exposes (library / service / CLI / bindings),
     stability promise, error-handling contract. -->
<api-overview>

## <library | service | CLI> surface

<!-- Choose the heading that matches the component. Templates for
     each form follow; delete the ones that don't apply. -->

### Library form

<!--
  For each public symbol: signature, summary, parameter docs,
  return docs, error cases, example. Use the language's idiomatic
  doc syntax. Cross-link to docs/architecture.md for context.
-->
```
<language-specific-signature>
```

- **Summary:** <one-sentence>
- **Parameters:** <param-docs>
- **Returns:** <return-docs>
- **Errors:** <error-cases>

### Service form

<!--
  For each endpoint: method, path, request body, response body,
  auth requirements, status codes.
-->
- `<method> <path>` — <one-sentence>
  - **Auth:** <auth-requirement>
  - **Request:** `<schema-or-example>`
  - **Response:** `<schema-or-example>`
  - **Errors:** `<status-codes-and-meaning>`

### CLI form

<!--
  For each command: name, summary, options, exit codes, env vars,
  example. The CLI manual is exhaustive — every flag is here.
-->
- `<tool> <command> [options]` — <one-sentence>
  - **Options:** <flags-and-defaults>
  - **Exit codes:** <codes-and-meaning>
  - **Environment:** <env-vars>
  - **Example:** `<command-line>`

## Error model

<!-- The component's error contract. For libraries: error type,
     variants, when each is returned. For services: status-code
     conventions, error-body shape. For CLIs: exit-code conventions
     and what stderr looks like. -->
<error-model-description>

## Stability and versioning

<!-- What "public" means for this component, what is allowed to
     change between minor versions, breaking-change policy. The
     concrete versioning scheme comes from the language-pack. -->
- **Public surface:** <what-is-covered-by-semver>
- **Versioning:** see the language-pack `Release & versioning`
  section.
