<!--
  Language-pack: Rust (2024 edition).
  Governed by: BLUEPRINT-SPEC.md §5
  Source of conventions: extracted from lived practice in the
  Sodium repo (CLAUDE.md, Cargo.toml, deny.toml, README.md). All
  references to specific Sodium domain concepts have been
  generalized away.
-->

# Language-Pack: Rust

## Build manifest

- **Manifest:** `Cargo.toml` at the repo root.
- **Workspace:** workspaces are the default layout. A typical
  component splits into `<component>-core` (library) and, when
  applicable, `<component>-cli` (binary) plus any other crates.
- **Edition:** `2024`.
- **Resolver:** `3` (edition 2024's default, makes feature
  unification predictable).
- **Toolchain:** stable, installed via [rustup](https://rustup.rs/).
  Pin a minimum-supported Rust version (MSRV) in
  `rust-toolchain.toml` only if downstream consumers need it.
- **License:** declare it explicitly in `[workspace.package]`.

Example skeleton:

```toml
[workspace]
resolver = "3"
members = ["<component>-core", "<component>-cli"]

[workspace.package]
version = "0.1.0"
edition = "2024"
license = "<license>"
```

## Module / package layout

```
<component>/
├── Cargo.toml                          # workspace manifest
├── Cargo.lock                          # checked in for binaries; check
│                                       # in for libraries too unless the
│                                       # component is published to a
│                                       # registry as a library only
├── <component>-core/
│   ├── Cargo.toml
│   └── src/
│       ├── lib.rs                      # `#![deny(warnings)]` + module decls
│       ├── <module-1>/
│       │   ├── mod.rs                  # public API of this module
│       │   └── <submodule>.rs
│       └── <module-2>/...
├── <component>-cli/                    # only if the component has a CLI
│   ├── Cargo.toml
│   └── src/main.rs
├── tests/                              # workspace-wide integration tests
└── examples/                           # runnable usage demos (optional)
```

One module per logical responsibility, not per type. Multiple
`impl` blocks for one type can live in one file.

## Test position convention

- **Unit tests:** colocated with the code under test, in a
  `#[cfg(test)] mod tests { … }` block at the bottom of the file.
- **Integration tests:** under `tests/<scenario>.rs`. One file
  per scenario.
- **Doc-tests:** in `///` doc-comments on public items where the
  example is short enough to be the documentation. They run as
  part of `cargo test`.
- **Examples:** under `examples/<name>.rs` for runnable usage
  demos that aren't tests. Built with `cargo build --examples`.

## Language-neutral operations → concrete commands

| Language-neutral operation | Concrete command |
|---|---|
| `pre-commit smoke`     | `cargo fmt --all -- --check && cargo clippy --workspace --all-targets -- -D warnings && cargo test --workspace --no-fail-fast && cargo deny check` |
| `pre-merge gate`       | `pre-commit smoke && cargo build --workspace --release` |
| `lint`                 | `cargo clippy --workspace --all-targets -- -D warnings` |
| `format`               | `cargo fmt --all` |
| `format-check`         | `cargo fmt --all -- --check` |
| `unit-tests`           | `cargo test --workspace --lib --no-fail-fast` |
| `integration-tests`    | `cargo test --workspace --test '*' --no-fail-fast` |
| `dep-license-check`    | `cargo deny check` |
| `api-docs-generate`    | `cargo doc --workspace --no-deps` *(add `RUSTDOCFLAGS="-D warnings"` to fail on broken intra-doc links)* |
| `release-build`        | `cargo build --workspace --release` |

The `pre-commit smoke` line is the non-negotiable gate. All four
sub-commands must pass — a subset is not enough. `cargo deny`
guards license drift and the RustSec advisory feed; install it
once per machine via `cargo install --locked cargo-deny`.

Add `#![deny(warnings)]` (and `#![deny(clippy::all)]` /
`#![deny(clippy::pedantic)]` if the component takes the strict
posture) at the top of `lib.rs` so warnings break local builds,
not just CI.

## Error-handling idiom

- **Public surface:** return `Result<T, E>` with a component-owned
  error enum. Use `thiserror` to derive the enum. Do **not** use
  `anyhow` in library code — `anyhow` erases the error type and
  pushes the categorization burden onto callers. `anyhow` is fine
  in binary crates (CLI tools, examples) where the only consumer
  is a human reading stderr.
- **Propagation:** the `?` operator. Conversions are encoded via
  `#[from]` on the `thiserror` enum.
- **No `.unwrap()` outside tests.** `unwrap()` is acceptable in
  `#[cfg(test)]` blocks because a panicking test is a failing
  test. In library code it is a defect.
- **No silent error swallowing.** `let _ = result;` on a fallible
  call is a code-smell; either handle the error or document why
  ignoring it is correct.

## Common pitfalls

- **Edition 2024 + `#[no_mangle]`:** edition 2024 requires
  `#[unsafe(no_mangle)]` (the `unsafe` attribute is now mandatory
  on the macro, even when applied to `extern "C"` functions).
  UniFFI-generated code already does this; if you hand-write FFI,
  make sure you do too.
- **`unsafe` is forbidden in hand-written code.** `unsafe`
  bypasses the borrow checker, which is exactly the guarantee
  Rust is selected for. Generated code (UniFFI scaffolding) is
  exempt; hand-written `unsafe fn`, `unsafe { ... }`, `transmute`,
  raw pointer arithmetic are not. A `grep -rn 'unsafe' src/` in
  hand-written code should return only generated-code hits.
- **Custom macros are forbidden.** `macro_rules!`, proc-macros,
  custom derive macros hide control flow and defeat IDE
  navigation. Standard-library macros (`println!`, `assert!`,
  `format!`) and standard derives (`Debug`, `Clone`,
  `PartialEq`) are fine. `#[derive(uniffi::...)]` is fine.
- **`println!` in library code.** Use `tracing` (`debug!`,
  `info!`, `warn!`, `error!`) in library crates. `println!` /
  `eprintln!` are appropriate only in binary crates whose stdout
  / stderr is the user-facing interface (CLIs).
- **Function-length drift.** A function over ~40 lines is
  almost always two responsibilities. Decompose it. Limit to 40
  lines as a hard rule, with 60 reserved for justified exceptions
  (e.g. a large `match` with many one-line arms).
- **File-length drift.** A file over ~200 code-lines (excluding
  tests and comments) hosting more than one logical
  responsibility should be split. The trigger is responsibility,
  not raw line count: a single-concern 300-line file is fine; a
  two-concern 180-line file is not.
- **`Cargo.lock` in libraries.** Check it in. The historical
  guidance against committing `Cargo.lock` for libraries
  predates the modern reproducible-build expectation; checking
  it in keeps builds deterministic for contributors and CI.
- **Multi-version pulls.** `cargo tree --duplicates` flushes
  them out. `cargo deny`'s `[bans] multiple-versions = "warn"`
  surfaces them in the gate without blocking.

## Recommended dependencies

| Concern | Library | Rationale |
|---|---|---|
| logging        | `tracing` + `tracing-subscriber` | Structured, async-aware, the de-facto standard for library logging. |
| testing        | built-in `cargo test` + `proptest` for property tests | Idiomatic, no extra runtime. |
| HTTP mocking   | `httpmock` (dev-dep) | Local mock server, no nightly. |
| serialization  | `serde` + `serde_json` (or `ciborium` for CBOR) | The Rust serde ecosystem is the universal answer. |
| HTTP client    | `reqwest` with `rustls-tls` (no `native-tls`) | Pure-Rust TLS stack, predictable cross-compile. |
| async runtime  | `tokio` 1.x | The dominant async runtime; UniFFI integrates cleanly via `tokio` feature. |
| error enums    | `thiserror` | Boilerplate-free derives for library error types. |
| FFI bindings   | `uniffi` 0.29+ | Generates Kotlin / Swift / WASM bindings from a UDL or proc-macro spec. |
| license gate   | `cargo-deny` | License allow-list + advisory feed in one tool. |
| third-party-license report | `cargo-about` | Generates a single HTML / text report aligned with `deny.toml`. |
| cross-compile (Android) | `cargo-ndk` | Wraps `cargo` with the Android NDK toolchain. |
| WebAssembly    | `wasm-pack` | Standard packaging for WASM-targeted crates. |

The list captures the lived stack; deviate when the component
genuinely needs something different, not as a stylistic preference.

### Starter `deny.toml`

`cargo-deny` blocks copyleft licenses from sneaking in
transitively and surfaces unfixed CVEs in the dependency graph.
The following `deny.toml` is a sensible default — copy it to the
component repo root and trim or extend the allow-list as needed:

```toml
# Run via `cargo deny check`. Part of the pre-commit smoke gate.

[graph]
all-features = false

[output]
feature-depth = 1

# ---------------------------------------------------------------
# Licenses — only permissive licenses pass by default. Any GPL,
# AGPL, or other copyleft license in the transitive graph fails
# the build. Add an `exceptions` block per crate if you need to
# allow a specific copyleft dep temporarily.
# ---------------------------------------------------------------
[licenses]
allow = [
    "Apache-2.0",
    "MIT",
    "BSD-3-Clause",
    "BSD-2-Clause",
    "ISC",
    "MPL-2.0",
    "Unicode-DFS-2016",
    "Unicode-3.0",
    "CC0-1.0",
    "Zlib",
    # webpki-roots (Mozilla's root-CA list, pulled in via reqwest's
    # rustls stack) uses this permissive license.
    "CDLA-Permissive-2.0",
]
# 1.0 = only accept crates whose license file matches a canonical
# license exactly. Lower values drop the bar; not appropriate for
# security-sensitive crates.
confidence-threshold = 0.93

# ---------------------------------------------------------------
# Advisories (RustSec feed). Unfixed CVEs in a dependency are a
# ship-stopper, not a follow-up.
# ---------------------------------------------------------------
[advisories]
version = 2
yanked = "deny"
# `unmaintained` defaults to "all" → all unmaintained advisories
# are blocking. Downgrade via the `ignore` list on a case-by-
# case basis with a one-line reason.
ignore = [
    # { id = "RUSTSEC-YYYY-NNNN", reason = "transitive via <dev-dep>; not in shipped binaries" },
]

# ---------------------------------------------------------------
# Bans — surface duplicate-version pulls and forbid wildcard
# version requirements.
# ---------------------------------------------------------------
[bans]
multiple-versions = "warn"
wildcards = "deny"
allow-wildcard-paths = true   # workspace path-deps are wildcards; legitimate
highlight = "all"
workspace-default-features = "allow"
external-default-features = "allow"

# ---------------------------------------------------------------
# Sources — only the canonical crates.io registry by default.
# Git-source dependencies trigger a warning so any new pull is
# investigated before allow-listing.
# ---------------------------------------------------------------
[sources]
unknown-registry = "deny"
unknown-git = "warn"
allow-registry = ["https://github.com/rust-lang/crates.io-index"]
allow-git = []
```

Keep `deny.toml` and `about.toml` (for `cargo about generate`)
in sync — any drift between the two is a review defect. Both
sit at the component repo root.

## Release & versioning

<!-- TBD: this section reflects Rust community defaults; the
     blueprint's source repo (Sodium) has not yet had a tagged
     release at the time of writing, so the exact tag-and-publish
     flow is generic rather than lived practice. Tracked in
     OPEN-QUESTIONS.md. -->

- **Versioning:** SemVer per Cargo's standard. Bump rules:
  - **0.x:** any breaking change is a minor bump (`0.4.0`); a
    bug-fix is a patch (`0.3.1`). Pre-1.0 has no public-API
    stability promise.
  - **1.x and beyond:** breaking change → major; backward-
    compatible feature → minor; bug fix → patch.
- **Workspace versions:** keep all member crates of a workspace
  on the same version unless an ADR explicitly justifies
  divergence.
- **Tags:** `v{MAJOR}.{MINOR}.{PATCH}` annotated tags.
  Pre-release tags use `v{MAJOR}.{MINOR}.{PATCH}-rc.{N}`.
- **Publish flow** (when the component publishes to crates.io):
  1. Bump `[workspace.package].version`.
  2. Update `CHANGELOG.md` (or equivalent) in the same commit.
  3. Run `pre-merge gate`.
  4. `git tag -a v{X.Y.Z} -m "release {X.Y.Z}"`.
  5. `cargo publish -p <member-crate>` per crate, in
     dependency order.
- **Release artifacts:** `cargo build --release` produces
  optimized binaries; UniFFI bindings are produced separately by
  the binding-generation step (see Cross-compilation below).

## Cross-compilation targets *(optional)*

For components that ship to mobile / web / desktop:

| Target          | Toolchain                    | Notes |
|-----------------|------------------------------|-------|
| Android         | `cargo-ndk` + Android NDK    | Generates `.so` per ABI; consumed via UniFFI Kotlin bindings. |
| iOS / macOS     | `aarch64-apple-ios`, `aarch64-apple-darwin`, etc. | Built with the Apple toolchain; UniFFI emits Swift bindings. |
| WebAssembly     | `wasm-pack` + `wasm32-unknown-unknown` | Browser / Node consumers; UniFFI emits TypeScript glue. |
| Linux x86_64    | default stable               | CI baseline. |

If the component publishes UniFFI bindings, document the
binding-generation command (`uniffi-bindgen generate ...` or the
proc-macro-driven equivalent) here.

## IDE / editor setup *(optional)*

<!-- TBD: not yet codified. Likely: `rust-analyzer` everywhere;
     enable `clippy` on save; format-on-save with `rustfmt`;
     fail-fast on `unsafe` highlighting. To be elaborated as
     teams converge on a recommendation. -->

- `rust-analyzer` is the recommended language server.
- `rustfmt` on save and `clippy` on save are the minimum useful
  configuration; both come with the default toolchain.
