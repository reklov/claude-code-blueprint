<!--
  This file lives in the COMPONENT root after instantiation. It is
  the manual checklist for finishing the bootstrap; once §10 below
  is done, delete this file from the component repo (spec §3 / §4.10
  marks BOOTSTRAP.md as transient).

  Spec governance: BLUEPRINT-SPEC.md §7 (Bootstrap Process).
-->

# Bootstrap — finish setting up this component

You are reading this from the **component repo** that was just
created by copying `core/` (and the chosen language-pack) out of
the `claude-code-blueprint` repo. Follow the steps below, then
delete this file.

## §0. Before you start

A short pre-flight checklist. Decide each item *before* you start
replacing placeholders — half-decided answers waste two passes.

- [ ] **Language pack chosen** — one of `rust`, `go`, `typescript`,
      `kotlin` (or, for spec §6.3 polyglot, a primary + secondary
      set). The blueprint repo has populated packs for `rust` and
      `go` today.
- [ ] **Component name in kebab-case decided** (e.g.
      `inventory-service`). This becomes the directory name, the
      `{{COMPONENT-NAME}}` mustache value, and the prefix of every
      branch.
- [ ] **License decided** — see the SPDX cheat-sheet under §1.
- [ ] **Genesis decision drafted in one line** — what's the very
      first architectural commitment for this component? (E.g.
      "use Rust 2024 with UniFFI bindings", "Go service with PostgreSQL
      backend".) ADR-001 will document it.
- [ ] **First step titled** — what's Step 001's headline? (Often
      "Skeleton + first integration test" or similar minimum-viable
      vertical slice.)
- [ ] **Git remote available** — you know where the component repo
      will be pushed and have write access. If not yet, you can
      still bootstrap locally and add the remote in §6.
- [ ] **Language toolchain installed.**
      - Rust: `rustup` + `cargo install --locked cargo-deny`.
      - Go: current Go SDK, `go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest`.
      - TypeScript / Kotlin: see the respective pack when it exists.

## §1. Confirm the bootstrap answers

Verify (or write down, if not already decided) the values that
will replace the bootstrap-mustache placeholders. These are
the only `{{...}}` substitutions a bootstrap tool would do
automatically (per BLUEPRINT-SPEC.md §3).

| Question | Format | Example |
|---|---|---|
| `COMPONENT-NAME`              | kebab-case identifier        | `inventory-service` |
| `COMPONENT-TITLE`             | human-readable name          | `Inventory Service` |
| `OWNER`                       | person or team               | `Volker Zöpfel` |
| `LANGUAGE-PACK`               | `rust` \| `go` \| `typescript` \| `kotlin` (or, for spec §6.3 polyglot, primary + secondary list) | `rust` |
| `LICENSE`                     | SPDX identifier              | `Apache-2.0` |
| `GENESIS-DECISION-TITLE`      | short title for ADR-001      | `Use Rust 2024 with UniFFI bindings` |
| `genesis-decision-slug`       | kebab-case slug for ADR-001  | `use-rust-2024-with-uniffi-bindings` |
| `FIRST-STEP-TITLE`            | title for the seed step      | `Skeleton + first integration test` |
| `first-step-slug`             | kebab-case slug for step 001 | `skeleton-integration-test` |

### SPDX cheat-sheet for the `LICENSE` answer

The `LICENSE` value goes into `Cargo.toml` / `go.mod` / `LICENSE`
file headers. Use an exact SPDX identifier from
<https://spdx.org/licenses/>. Common choices:

| SPDX identifier | When to pick it |
|---|---|
| `Apache-2.0`           | Permissive with explicit patent grant. Default for OSS components that may attract third-party contributors. (Sodium uses this.) |
| `MIT`                  | Permissive, minimal. Slightly simpler than Apache-2.0; no explicit patent clause. |
| `MPL-2.0`              | Weak copyleft at the file level. Useful for libraries whose modifications should stay open but which may be combined with proprietary code. |
| `BSD-3-Clause`         | Permissive, similar to MIT plus a no-endorsement clause. |
| `LicenseRef-SchwarzIT-Internal` | Internal-only, no OSS use. Use this `LicenseRef-…` form when no SPDX identifier applies; document the actual terms in the `LICENSE` file. |

Distinct from these, every `<...>` placeholder is **pattern
notation**. It stays in the file as-is and is filled in by the
developer when each new step / ADR / finding / branch / commit is
created. Do not blanket-replace `<...>`.

## §2. Verify the language-pack is in place

```bash
ls docs/_language-pack-<chosen-language>.md
```

If missing, copy it. The command below assumes the
`claude-code-blueprint` repo is a sibling directory to this
component repo (the most common layout):

```bash
cp ../claude-code-blueprint/language-packs/<chosen-language>.md \
   docs/_language-pack-<chosen-language>.md
```

If the blueprint lives elsewhere, adjust the source path
accordingly — or clone it freshly into a temp directory and copy
from there:

```bash
git clone <private-remote>/claude-code-blueprint /tmp/bp
cp /tmp/bp/language-packs/<chosen-language>.md \
   docs/_language-pack-<chosen-language>.md
rm -rf /tmp/bp
```

For polyglot components per spec §6.3, copy each secondary pack
as `docs/_language-pack-<secondary>.md` and adjust `CLAUDE.md`
§"Build, test, smoke" to aggregate them.

## §3. Replace the bootstrap mustaches

Every `{{...}}` placeholder in this directory is greppable:

```bash
grep -rn '{{' .
```

Replace each one with the value from §1. Do this systematically;
afterwards, the same grep should return zero hits except inside
explanatory HTML comments (templates carry a literal `{{…}}`
example string in their header to document the convention — that
is intentional, not a leftover).

> **Do not touch `<...>`.** Pattern notation stays in the repo;
> developers fill those in per-instance over the component's life.

## §4. Rename the seed files

```bash
git mv docs/plan/001-{{first-step-slug}}.md \
       docs/plan/001-<your-first-step-slug>.md

git mv docs/adrs/001-{{genesis-decision-slug}}.md \
       docs/adrs/001-<your-genesis-decision-slug>.md
```

Replace `<your-first-step-slug>` and `<your-genesis-decision-slug>`
with the actual slug values from §1. Update cross-references — at
minimum the PLAN.md status row link and any links inside ADR-001 /
step 001.

## §5. Bind the language-pack into CLAUDE.md

`CLAUDE.md` §"Build, test, smoke — language-pack reference"
already points at `docs/_language-pack-{{language-pack}}.md`.
After §3's substitution it points at the real file. Verify the
link resolves.

## §6. Initialise git

```bash
git init
git add .
git commit -m "chore: bootstrap component from blueprint"
```

The first commit is `chore: bootstrap component from blueprint`
on `main`. **No production code is written before this commit.**
ADR-001 lands in `Proposed` status; flip it to `Accepted` when
the team has ratified it.

Commit-message prefix convention going forward (see
`CLAUDE.md` §Conventions): `feat(step-<NNN>): …` for behaviour
changes, `chore(step-<NNN>): …` for tidyings and infrastructure,
`fix(step-<NNN>): …` for bug fixes. Per the Tidy-First hard rule
in `CLAUDE.md`, tidyings live in their own commits, separate from
behaviour changes.

## §7. Verify the layout

Run the following snippet — it collects all failures and reports
them in a single summary instead of bailing at the first one:

```bash
fails=()

# Required root files.
for f in CLAUDE.md PLAN.md README.md FINDINGS.md BOOTSTRAP.md \
         .gitignore .claude/settings.json reference/README.md; do
  [ -f "$f" ] || fails+=("missing file: $f")
done

# Required directories.
for d in docs/plan docs/adrs docs/findings reference .claude; do
  [ -d "$d" ] || fails+=("missing dir: $d")
done

# Required docs/ files.
for f in docs/HANDOFF.md docs/architecture.md docs/threat-model.md \
         docs/api.md \
         docs/plan/README.md docs/plan/_template.md \
         docs/adrs/README.md docs/adrs/_template.md \
         docs/findings/README.md docs/findings/_template.md; do
  [ -f "$f" ] || fails+=("missing file: $f")
done

# Language pack present.
ls docs/_language-pack-*.md > /dev/null 2>&1 \
  || fails+=("no language pack under docs/_language-pack-*.md")

# No leftover bootstrap mustaches (excluding the literal {{…}}
# example strings inside HTML comments of _template.md files).
leftover=$(grep -rn '{{' . \
  --exclude-dir=.git \
  --exclude=BOOTSTRAP.md \
  | grep -vE '_template\.md:.*\{\{[…\.]+\}\}' \
  | grep -v '{{...}}')
if [ -n "$leftover" ]; then
  fails+=("leftover {{...}} placeholders:")
  fails+=("$leftover")
fi

# Three-digit numbering.
bad=$(find docs -type f -name "[0-9]*-*.md" \
  | grep -vE 'docs/(plan|adrs|findings)/[0-9]{3}-')
[ -z "$bad" ] || fails+=("non-3-digit numbering: $bad")

# Report.
if [ ${#fails[@]} -eq 0 ]; then
  echo "OK: layout verified"
else
  echo "FAIL: ${#fails[@]} issue(s)"
  printf '  - %s\n' "${fails[@]}"
  exit 1
fi
```

## §8. Run the language-pack smoke

`pre-commit smoke` (defined in your language-pack) must pass
before the bootstrap commit is considered complete. For Rust:

```bash
cargo fmt --all -- --check && \
  cargo clippy --workspace --all-targets -- -D warnings && \
  cargo test --workspace --no-fail-fast && \
  cargo deny check
```

For Go:

```bash
test -z "$(gofmt -l .)" && \
  go vet ./... && \
  golangci-lint run && \
  go test -short ./...
```

(There is no production code yet, so this is mainly a toolchain
check — but it confirms the gate is in place.)

## §9. First real work

Open `docs/plan/001-<your-first-step-slug>.md`. Fill in the **TDD
list** before creating the step branch. The first non-bootstrap
commit is the failing tests — review with the user, then
implement. From here on, the conventions in `CLAUDE.md` are
authoritative.

## §10. Delete this file

```bash
git rm BOOTSTRAP.md
git commit -m "chore: drop BOOTSTRAP.md (component bootstrap complete)"
```

Per spec §3 / §4.10 BOOTSTRAP.md is transient. Once the steps
above are done, it has no further purpose in the component repo.
