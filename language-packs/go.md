<!--
  Language-pack: Go (1.22+).
  Governed by: BLUEPRINT-SPEC.md §5
  Source of conventions: Go's official guidance (Effective Go,
  the Go blog, the standard library), the consensus tooling
  ecosystem (golangci-lint, goreleaser, go-licenses), and the
  contested-but-pragmatic project-layout common practice. No
  first-party Schwarz Digits Go component has shipped at the
  time of writing; this pack will be updated in place once one
  does, and the TBD-marked sections elaborated.
-->

# Language-Pack: Go

## Build manifest

- **Manifest:** `go.mod` at module root.
- **Lockfile:** `go.sum` — checked in. `go mod tidy` keeps it
  current.
- **Workspace:** for multi-module repos use `go.work` (Go 1.18+).
  Single-module is the default and what most components want.
- **Toolchain:** Go 1.22 minimum. The `go` directive in `go.mod`
  pins the compatibility floor (and unlocks the per-iteration
  loop-variable scope fix); CI runs the latest stable.
- **License:** declared via the `LICENSE` file at repo root and
  an `// SPDX-License-Identifier: <license>` line at the top of
  each source file. Go has no Cargo-style license field inside
  `go.mod`.

Example skeleton (`go.mod`):

```
module github.com/<org>/<component>

go 1.22

require (
    // pinned by `go mod tidy`
)
```

For binary components, a `cmd/<component>/main.go` entry point.
For library components, the public API lives in the module root
or in deliberately scoped sub-packages.

## Module / package layout

```
<component>/
├── go.mod                           # module manifest
├── go.sum                           # dependency checksums (committed)
├── LICENSE
├── cmd/                             # binary entry points
│   └── <component>/
│       └── main.go
├── internal/                        # compiler-enforced private packages
│   ├── <module-1>/
│   │   ├── <module-1>.go
│   │   └── <module-1>_test.go       # tests colocated with subject
│   └── <module-2>/...
├── <public-package>/                # public packages at module root
│   └── ...
└── docs/                            # blueprint-mandated docs
```

Conventions:

- **One package per directory.** The directory name is the
  package name (lowercase, single word preferred — no
  underscores or mixed case).
- **`internal/` is compiler-enforced.** Code outside the module
  root cannot import packages under `internal/`. Use it for
  everything that is not part of the public API.
- **`pkg/` is not a blueprint convention.** The Go community is
  split on it; the modern lean is to put public packages
  directly at module root and use `internal/` for everything
  else. If you choose `pkg/`, document the rationale in
  `docs/architecture.md`.
- **One responsibility per package**, not one type per package.

## Test position convention

- **Unit tests:** colocated with the code under test. `foo.go`
  is tested by `foo_test.go` in the same package.
- **Black-box tests:** when you want to exercise only the public
  API, use `package <pkg>_test` in `foo_test.go` (same
  directory, distinct package name).
- **Integration tests:** same `*_test.go` convention, gated with
  a `//go:build integration` build tag at the file top. Run via
  `go test -tags=integration ./...`.
- **Examples:** `func ExampleFoo()` in `*_test.go`. Validated as
  tests *and* rendered in godoc / pkg.go.dev.
- **Benchmarks:** `func BenchmarkFoo(b *testing.B)`. Run with
  `go test -bench=. -benchmem`.
- **Fuzz tests** (Go 1.18+): `func FuzzFoo(f *testing.F)`. Run
  with `go test -fuzz=FuzzFoo`.

No `tests/` directory at the module root by Go convention.

## Language-neutral operations → concrete commands

| Language-neutral operation | Concrete command |
|---|---|
| `pre-commit smoke`     | `test -z "$(gofmt -l .)" && go vet ./... && golangci-lint run && go test -short ./...` |
| `pre-merge gate`       | `pre-commit smoke && go test -race ./... && go test -tags=integration ./...` |
| `lint`                 | `golangci-lint run` |
| `format`               | `gofmt -w .` *(or `goimports -w .` to also sort imports)* |
| `format-check`         | `test -z "$(gofmt -l .)"` |
| `unit-tests`           | `go test -short ./...` |
| `integration-tests`    | `go test -tags=integration ./...` |
| `dep-license-check`    | `go-licenses check ./...` |
| `api-docs-generate`    | `pkgsite -http=:6060` *(local godoc viewer; pkg.go.dev is the public render)* |
| `release-build`        | `go build -trimpath -ldflags="-s -w" ./cmd/...` |

`pre-commit smoke` is the non-negotiable gate. Notes:

- `gofmt -l .` returns the list of mis-formatted files; `test -z`
  fails the gate when that list is non-empty.
- `go vet` is mandatory — it catches bug classes the compiler
  alone does not (printf format mismatches, copied locks,
  unreachable code, struct-tag typos).
- `golangci-lint` is the umbrella linter runner. Configure it
  via `.golangci.yml` at repo root. The default-enabled set
  (errcheck, govet, ineffassign, staticcheck, unused) is the
  minimum useful posture; add `gocritic`, `gosec`, `revive` on a
  per-component basis.
- `-race` doubles binary size and slows the suite ~2-3×. Run on
  every pre-merge gate, not on every save.
- `-trimpath` in `release-build` strips local filesystem paths
  from the binary — improves reproducibility and avoids leaking
  build-host paths.

### `scripts/` directory convention

If the smoke or live-validation step is more than a single
shell command, factor it into a script under `scripts/` at the
component repo root and call the script from the operations
table above. The convention:

- `scripts/smoke.sh` — pre-commit smoke, if it doesn't fit on
  one line.
- `scripts/smoke-baseline.sh` — live-regression smoke against a
  real staging environment.
- `scripts/<other>.sh` — repeatable operational tasks (release
  cut, dependency-license report regeneration, etc.).

All scripts are POSIX-compatible bash, executable bit set,
documented with a header comment block stating the script's
purpose and exit-code semantics. The `pre-commit smoke` row in
the operations table then reads `scripts/smoke.sh` instead of
the inline chain.

## Error-handling idiom

- **Public surface:** functions return `(T, error)`. The caller
  checks `if err != nil` and either handles or propagates. This
  is non-negotiable in idiomatic Go.
- **Wrapping:** `fmt.Errorf("context: %w", err)` preserves the
  original error and adds context. The `%w` verb makes the
  wrapped error inspectable via `errors.Is` / `errors.As`.
- **Sentinels:** `var ErrNotFound = errors.New("not found")` for
  errors callers want to compare with `errors.Is`.
- **Typed errors:** define a struct type with an `Error() string`
  method when callers need structured fields. Inspect via
  `errors.As(err, &target)`.
- **No panics in library code.** `panic` is reserved for truly
  unrecoverable invariant violations. A library that panics on
  bad input is a library bug. Document any deliberate `panic`.
- **Avoid silent error drops.** `_ = err` or unchecked returns
  are a code smell beyond a few legitimate cases (`defer
  f.Close()` on a read-only file, `_ = w.Write(...)` to a
  `*bytes.Buffer`). When you do drop an error, document why in a
  one-line comment.

## Common pitfalls

- **Nil-channel block.** Reading from or writing to a nil channel
  blocks forever (this is a deliberate feature for `select`, a
  bug for direct use). Always `make` channels before use.
- **Nil-map write panic.** `var m map[string]int; m["a"] = 1`
  panics. Always `make(map[K]V)` (or use a literal) before
  writing.
- **Goroutine leaks.** A goroutine that never returns is a leak.
  Provide a cancellation path: `context.Context` for I/O,
  `chan struct{}` for explicit signal, closed input channel for
  range-over-channel.
- **Range-loop variable capture.** Pre-1.22, the loop variable
  was reused — closures captured the same address. Go 1.22+
  gives each iteration a fresh variable. Set `go 1.22` (or
  higher) in `go.mod` to opt in; legacy code may still carry
  the `v := v` workaround inside the loop body.
- **`defer` in loops.** `defer` runs at function exit, not
  loop-iteration exit. Wrap the iteration body in a closure if
  you want per-iteration cleanup.
- **Slice aliasing.** `b := a[:0]; b = append(b, x)` may
  overwrite `a`'s backing array. `slices.Clone` (Go 1.21+) is
  the safe copy.
- **Map iteration order is randomized.** Intentional. Sort the
  keys explicitly when you need a deterministic order.
- **`interface{}` / `any` loses type safety.** Use generics
  (Go 1.18+) when the operation is parametric over a type.
- **HTTP body not closed.** `defer resp.Body.Close()` after
  `http.Get`/`Do` — every time. Forgetting leaks connections
  back into the transport pool.
- **`time.After` in long-running selects.** Each call allocates
  a timer that lives until firing. In a hot loop, that's a
  leak. Use `time.NewTimer` with manual `Reset`/`Stop`.
- **CGO breaks cross-compilation.** Pure Go cross-compiles
  trivially (`GOOS=linux GOARCH=arm64 go build`); CGO needs a
  cross-toolchain matching the target. Avoid CGO unless
  necessary.
- **Unused imports / variables are compile errors** (intentional
  — keeps the build clean). **Unused module dependencies** are
  not flagged by default; `go mod tidy` removes them.

## Recommended dependencies

| Concern | Library | Rationale |
|---|---|---|
| logging        | `log/slog` (stdlib 1.21+) | Structured logging in the standard library; no third-party dep needed. |
| testing        | `testing` (stdlib) + `github.com/stretchr/testify` | `testing` is stdlib; `testify`'s `assert` / `require` are de-facto for ergonomic assertions. |
| HTTP mocking   | `net/http/httptest` (stdlib) | Spins up a real server in-process; sufficient for almost every case. |
| JSON           | `encoding/json` (stdlib) | Stdlib. |
| Protobuf       | `google.golang.org/protobuf` | Official Go protobuf v2 module; `gogo/protobuf` is unmaintained. |
| HTTP client    | `net/http` (stdlib) | Stdlib is sufficient. Wrap thinly when retries / circuit breaking are needed. |
| concurrency    | goroutines + `context.Context` (stdlib) | Built-in. `context` is the cancellation-and-deadline contract every public API takes as the first parameter. |
| CLI parsing    | `flag` (stdlib) for one-off; `github.com/spf13/cobra` for multi-command | `flag` is fine for single-binary tools; `cobra` is the canonical multi-command framework. |
| config         | env vars + `github.com/kelseyhightower/envconfig` | 12-factor style; envconfig wires env vars into a typed struct. |
| dep license    | `github.com/google/go-licenses` | Google-maintained; integrates as `go-licenses check ./...`. |
| release tooling | `github.com/goreleaser/goreleaser` | De-facto for cross-platform binary releases. |

Stdlib first; reach for a third-party only when stdlib falls
short. That is the Go community's standing posture and the
blueprint follows it.

## Release & versioning

- **Versioning:** SemVer. Go modules enforce SemVer at the
  module-path level for major-version transitions:
  - **0.x.y:** any breaking change is a minor bump (`0.4.0`).
    Pre-1.0 has no public-API stability promise.
  - **1.x.y → 2.x.y:** the import path itself changes — the
    module must declare `module github.com/<org>/<component>/v2`
    in `go.mod`. Without the `/vN` suffix, consumers cannot pin
    the new major; Go modules treats it as a different module.
  - **v3+ same rule:** `/v3`, `/v4`, etc.
- **Tags:** `v<major>.<minor>.<patch>` annotated tags.
  ```
  git tag -a v1.2.3 -m "release 1.2.3"
  git push origin v1.2.3
  ```
- **Pre-release tags:** `v1.2.0-rc.1`, `-alpha.1`, `-beta.1`. Go
  modules respects SemVer's pre-release ordering.
- **Publish flow:** Go modules pull through the proxy
  (`proxy.golang.org`), which observes the git remote. There is
  no separate "publish" step:
  1. Bump version mentions in source / docs / CHANGELOG.
  2. Run `pre-merge gate`.
  3. Tag and push the tag.
  4. Verify via `go list -m github.com/<org>/<component>@v1.2.3`.
- **Release artifacts (binary components):** `goreleaser` builds
  per-OS / per-arch archives plus checksums and (optionally)
  publishes them to GitHub Releases / a custom artifact store.
  Configure via `.goreleaser.yaml`.

## Cross-compilation targets *(optional)*

For components that ship binaries:

| Target                | Toolchain                             | Notes |
|-----------------------|---------------------------------------|-------|
| linux/amd64           | `GOOS=linux GOARCH=amd64 go build`    | CI baseline. |
| linux/arm64           | `GOOS=linux GOARCH=arm64 go build`    | Common in cloud / container deployments. |
| darwin/arm64          | `GOOS=darwin GOARCH=arm64 go build`   | Apple Silicon dev machines. |
| darwin/amd64          | `GOOS=darwin GOARCH=amd64 go build`   | Intel Macs still in fleet. |
| windows/amd64         | `GOOS=windows GOARCH=amd64 go build`  | When applicable. |
| WebAssembly (browser) | `GOOS=js GOARCH=wasm go build`        | Produces `.wasm` for browser consumption. |
| WebAssembly (WASI)    | `GOOS=wasip1 GOARCH=wasm go build`    | Server-side WASM runtimes (Wasmtime, Wasmer). |

Pure Go cross-compiles trivially; CGO requires a cross-toolchain
matching the target triple. Prefer pure Go when cross-compile is
in scope.

## IDE / editor setup *(optional)*

- **Language server:** `gopls`, the official Go language server.
- **VS Code:** the Go extension wraps `gopls` and the standard
  toolchain end-to-end.
- **GoLand:** JetBrains' commercial IDE; first-class Go support.
- **Format on save:** use `goimports` (not just `gofmt`) — it
  sorts imports, adds missing ones, and removes unused ones in
  the same pass.
- **Lint on save:** wire `golangci-lint` into your editor. The
  feedback loop is a meaningful productivity boost over running
  it only at commit time.
