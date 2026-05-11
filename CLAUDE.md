# Bootstrap mode — Claude reads this on first start

You are Claude Code, running in a freshly cloned
`claude-code-blueprint` repository. Your task is to **bootstrap a
new component repo** for the user by interactively gathering the
required answers, presenting a plan, and then transforming this
clone into a working component skeleton.

When the bootstrap is finished, this file (`CLAUDE.md` at the
repo root) is deleted — it is replaced by `core/CLAUDE.md`, which
becomes the new component's permanent CLAUDE.md. The user will
then restart Claude Code so that the new CLAUDE.md takes effect.

---

## Bootstrap workflow

Follow the six steps below in order. Do **not** skip ahead or
batch them — each step builds on the previous one and the user
needs to see the intermediate state.

### Step 1 — Pick the communication language

Greet the user **in English**:

> Hi — I'll walk you through bootstrapping a new Claude-Code
> component from this blueprint. First: which language should we
> use for our conversation during the bootstrap? (English, German,
> or another language you prefer.)
>
> Note: all committed content — code, comments, commit messages,
> docs, ADRs — stays English per the blueprint's hard rules. Only
> our chat during the bootstrap switches.

After the user answers, switch to that language for the rest of
the conversation. The questions below are written in English;
translate them into the chosen language when you ask them.
Committed content (file contents, commit messages, placeholder
values written into the repo) always stays English regardless of
the chat language.

### Step 2 — Ask the bootstrap questions

Ask the questions one at a time (not all at once), wait for the
answer, validate the format briefly, then move on. Show examples
and defaults where useful.

1. **`COMPONENT-NAME`** — kebab-case identifier for the
   component. Becomes the workspace name and the prefix of every
   branch. Example: `inventory-service`.
2. **`COMPONENT-TITLE`** — human-readable name. Appears in the
   H1 of README and CLAUDE.md. Example: `Inventory Service`.
3. **`OWNER`** — the person or team taking ownership. Example:
   `Volker Zöpfel` or `Schwarz IT — Platform Squad`.
4. **`LANGUAGE-PACK`** — pick from the populated packs available
   under `language-packs/` in this repo (check the directory; at
   the time of this CLAUDE.md `rust` and `go` are populated, plus
   an empty `_template.md`). For polyglot components per
   `BLUEPRINT-SPEC.md` §6.3 the user names one primary plus a
   list of secondaries.
5. **`LICENSE`** — SPDX identifier. Common picks:
   - `Apache-2.0` — permissive, explicit patent grant (Sodium
     uses this).
   - `MIT` — permissive, minimal.
   - `MPL-2.0` — weak copyleft at file level.
   - `BSD-3-Clause` — permissive plus no-endorsement clause.
   - `LicenseRef-SchwarzIT-Internal` — internal-only, no SPDX
     match. The actual terms live in the component's `LICENSE`
     file.

   Full list: <https://spdx.org/licenses/>.
6. **`GENESIS-DECISION-TITLE`** — short title for ADR-001.
   Typically the language / architecture choice itself, e.g.
   `Use Rust 2024 with UniFFI bindings` or `Go service with
   PostgreSQL backend`. Derive `genesis-decision-slug` as the
   kebab-case version (e.g. `use-rust-2024-with-uniffi-bindings`)
   and confirm with the user.
7. **`FIRST-STEP-TITLE`** — title for Step 001. Often a minimum
   vertical slice: `Skeleton + first integration test`,
   `Hello-world HTTP endpoint`, etc. Derive `first-step-slug` as
   the kebab-case version and confirm.

### Step 3 — Read the spec

Read `BLUEPRINT-SPEC.md` §3 (directory layout), §7 (bootstrap
process), and the section schema for any file you will touch
(§4.x). The spec is the contract; if anything below disagrees
with the spec, **the spec wins** — flag the discrepancy to the
user before continuing.

### Step 4 — Present the plan

Show the user a structured plan **before** doing anything. The
plan must list:

- **Mustache substitutions** — the table of `{{...}}` placeholders
  and the values from Step 2 that will replace them. Mention that
  `<...>` (angle-bracket pattern notation) stays untouched.
- **Files to be renamed** — the two seed files:
  - `core/docs/plan/001-{{first-step-slug}}.md` →
    `core/docs/plan/001-<first-step-slug>.md`
  - `core/docs/adrs/001-{{genesis-decision-slug}}.md` →
    `core/docs/adrs/001-<genesis-decision-slug>.md`
- **Files to be moved** — the chosen language-pack:
  - `language-packs/<chosen-pack>.md` →
    `core/docs/_language-pack-<chosen-pack>.md`
- **Files to be deleted** (these are blueprint-only artifacts
  that do not belong in the component repo):
  - `BLUEPRINT-SPEC.md` (top-level, the contract — stays in the
    blueprint repo, not in components)
  - `OPEN-QUESTIONS.md`
  - `language-packs/` (entire directory — only the chosen pack
    was copied into `core/docs/`)
  - `core/` (entire directory — its contents are promoted to the
    repo root in Step 5)
  - `README.md` (top-level — replaced by `core/README.md`)
  - `CLAUDE.md` (this file — replaced by `core/CLAUDE.md`)
- **Git operations** at the end:
  - Remove the blueprint's `.git/` directory (drops the blueprint
    history, since the new component starts fresh)
  - `git init -b main` for the new component
  - Stage everything and create the first commit:
    `chore: bootstrap component from blueprint`

Ask the user to confirm the plan. If they want changes, accept
them and re-present. Do not proceed without explicit confirmation.

### Step 5 — Execute

After confirmation:

1. **Replace all mustache placeholders.** Every `{{...}}` in the
   repository (excluding `BLUEPRINT-SPEC.md` and this `CLAUDE.md`
   — both are about to be deleted) maps to one of the answers
   from Step 2. Use literal-string replacement, file by file,
   to keep the diff reviewable. Do NOT touch `<...>` patterns —
   they stay in place per the spec's placeholder convention.
2. **Rename the two seed files** using `git mv` once the new
   repo's `.git/` exists (Step 5 below) — or rename now and let
   the first `git add` pick them up. Whichever you choose, make
   sure cross-references inside the renamed files (e.g. links
   from ADR-001 to step 001) also use the new filenames.
3. **Copy the chosen language-pack** into
   `core/docs/_language-pack-<chosen-pack>.md`.
4. **Promote `core/` to the repo root.** Move every file and
   subdirectory inside `core/` up one level to the repo root.
   `core/.claude/settings.json` becomes `.claude/settings.json`;
   `core/.gitignore` becomes `.gitignore` (overwriting the
   blueprint's top-level gitignore, which is intentional); etc.
5. **Delete the blueprint-only files** listed in the plan
   (`BLUEPRINT-SPEC.md`, `OPEN-QUESTIONS.md`, `language-packs/`,
   `core/` (now empty), the top-level `README.md`, and this
   `CLAUDE.md` itself).
6. **Drop the blueprint's git history.** Remove the `.git/`
   directory so the new component starts with a clean slate.
7. **Initialize a fresh git repo:** `git init -b main`.
8. **Stage and commit:**
   `git add . && git commit -m "chore: bootstrap component from blueprint"`.

Run the bootstrap verification at the end (the same check the
spec §7 mandates):

- No unsubstituted `{{...}}` placeholders remain anywhere in the
  repo (except inside HTML-comment example strings in
  `_template.md` files — the literal `{{…}}` example token is
  intentional documentation of the convention).
- All mandatory directories exist: `docs/plan/`, `docs/adrs/`,
  `docs/findings/`, `reference/`, `.claude/`.
- All mandatory files exist: `CLAUDE.md`, `PLAN.md`, `README.md`,
  `FINDINGS.md`, `docs/HANDOFF.md`, `docs/architecture.md`,
  `docs/threat-model.md`, `docs/api.md`, `docs/_language-pack-<pack>.md`,
  plus the README + `_template.md` files under each `docs/`
  subdirectory.
- File numbering is three-digit (`001-…`, no `0001-…` and no
  `01-…`).

Report any failures to the user. If the verification surfaces a
real defect, **stop and ask** — do not paper over it.

### Step 6 — Hand off

Once the first commit is made, tell the user (in the chosen
chat language):

> Bootstrap complete. The new component is committed on `main`
> at the freshly initialized git repository.
>
> **Please quit and restart Claude Code now.** Your component's
> permanent `CLAUDE.md` (formerly `core/CLAUDE.md`) is now at the
> repo root and will take effect on the next start. The
> bootstrap CLAUDE.md you've been talking to has been deleted —
> from here on, the component's own conventions and hard rules
> apply.
>
> After restart, your first task is documented in
> `docs/HANDOFF.md` and `docs/plan/001-<first-step-slug>.md`:
> write the TDD list for Step 001, then commit the failing tests
> on a `step/001-<slug>` branch before any implementation code.

---

## Failure recovery

If anything goes wrong mid-bootstrap (your crash, the user's
abort, a destructive operation that surprised the user), the
clean reset is:

```bash
cd ..
rm -rf <component-directory>
git clone <blueprint-remote> <component-directory>
cd <component-directory>
claude
```

i.e. nuke the partial clone and start fresh from the blueprint
remote. The blueprint repo is unaffected; only the in-progress
component directory is discarded.

---

## What is not your job

- Writing the first Step's TDD list. That is the user's first
  task after the restart, and the hard rule "Strict TDD always"
  means the test list is the user's contract with you, not
  something you generate without their input.
- Writing production code in the new component. Bootstrap stops
  at the empty skeleton.
- Pushing to a remote. Remote setup is the user's decision and
  happens after they confirm the bootstrap was successful.
- Touching the blueprint repo itself (the one you were cloned
  from). Anything you do here is local to this clone.
