# Open questions — Phase 2 output

Items that surfaced while extracting the blueprint from the
Sodium repo. Each one lists what was observed, what the spec
says, and what choice the skeleton made.

---

## 1. HANDOFF.md section names (`§0`, `§1`, …)

- **Spec (§4.3):** mandates the section names
  `§0 First action`, `§1 Brief from previous session`,
  `§2 Concrete first task`, `§3 Quick reference`,
  `§4 Update contract`, with explicit per-section line caps.
- **Sodium today:** `docs/HANDOFF.md` does not use this exact
  naming or numbering — it has free-form `## Quick reference`,
  `## What landed on the way to here`, etc.
- **Skeleton:** follows the spec verbatim.
- **Implication:** Sodium's HANDOFF needs a one-time migration
  to match — out of scope here, but worth recording.

## 2. CLAUDE.md section ordering

- **Spec (§4.1):** mandates a 10-section order: Reading order →
  Hard rules → User preferences → Architecture overview →
  Functional scope → Build, test, smoke → Conventions →
  Documentation freshness → Update policy.
- **Sodium today:** ~720 lines, ordered by topic rather than the
  spec's order (Goal, Why-not-Kalium, Stack, Architecture, Wire
  API, MLS Protocol, Functional Scope, TDD, Conventions, etc.).
- **Skeleton:** spec-mandated order. Sodium will diverge from
  the blueprint until migrated.

## 3. Findings format migration

- **Spec (§4.6):** index (`FINDINGS.md`) plus per-finding
  full-text under `docs/findings/`.
- **Sodium today:** single `findings.md` file, German prose,
  numbered sections. Spec §9 and the Phase 2 briefing both
  state the migration is **out of scope for Phase 2**.
- **Skeleton:** new index+full-text format only. No back-port.

## 4. `reference/README.md` does not exist in Sodium

- **Spec (§4.8):** mandates a `reference/README.md` explaining
  the read-only-submodule rule and listing each submodule with
  its rationale.
- **Sodium today:** has the `reference/` directory and
  `.gitmodules`, but no README inside it.
- **Skeleton:** provides the README template. Sodium needs to
  add one (out of scope here).

## 5. `.gitignore` differs

- **Spec (§4.9):** the only mandated ignore is
  `.claude/settings.local.json`; `.claude/settings.json` is
  checked in.
- **Sodium today:** `.gitignore` has `.claude/*`, ignoring
  *everything* under `.claude/`. Sodium does not commit a shared
  `settings.json`.
- **Skeleton:** follows the spec — `.claude/settings.local.json`
  is ignored, `.claude/settings.json` is committed.

## 6. `docs/api.md` vs split files

- **Spec (§4.7):** a single `docs/api.md` is the default; split
  into `docs/api-library.md` / `docs/api-cli.md` /
  `docs/api-bindings.md` is allowed when the component has
  multiple interface types.
- **Sodium today:** uses three separate files
  (`docs/library.md`, `docs/cli.md`, `docs/bindings.md`). It
  exercises the split-file form.
- **Skeleton:** ships single `docs/api.md` with an HTML comment
  noting the split-when-needed rule. The component owner
  splits if and when warranted.

## 7. Step-file domain sections

- **Spec (§4.5):** mandatory sections are Goal, Sub-steps,
  Public API surface, TDD list, Smoke test, Out of scope, Files
  touched.
- **Sodium today:** step files include domain-specific
  sections like `matrix-rust-sdk analog`, `Wire API v14
  endpoints`, `RFC 9420 sections`, `Store additions`, `CLI
  command`. These are project-specific add-ons.
- **Skeleton:** encodes only the spec's mandatory sections.
  Component owners add domain sections as needed; the
  blueprint does not prescribe them.

## 8. `release & versioning` in `rust.md` is partly TBD

- **Spec (§5):** required section.
- **Sodium today:** has not cut a tagged release yet, so the
  exact tag-and-publish flow is not lived practice.
- **`rust.md`:** documents Rust community defaults (Cargo
  SemVer, `vMAJOR.MINOR.PATCH` annotated tags, `cargo publish`
  flow) and marks the section
  `<!-- TBD: not yet practiced anywhere -->`. Update when the
  first component cuts a release.

## 9. `IDE / editor setup` in `rust.md` is partly TBD

- **Spec (§5):** optional, recommended.
- **Sodium today:** no codified team recommendation.
- **`rust.md`:** notes `rust-analyzer` + `rustfmt` /
  `clippy`-on-save as a starting point and marks the section
  TBD. Elaborate when the team converges.

## 10. Bootstrap automation — **resolved (v2, 2026-05-11)**

- **Spec (§10 OQ-1):** resolved. The bootstrap is Claude-Code-driven
  via a top-level `CLAUDE.md` in the blueprint repo. Neither a
  separate script nor a pure markdown checklist — the bootstrap
  path is the architecture itself.
- **`core/BOOTSTRAP.md`:** removed. The manual checklist is
  obsolete; Claude handles the substitution, file renames,
  language-pack copy, blueprint-only-file deletion, and the
  first commit.
- **Failure recovery:** nuke the partial clone and re-clone the
  blueprint. No idempotent re-runs to maintain.
- **No manual fallback** — if Claude Code is unavailable, a
  Claude-Code-driven project makes no sense in the first place.

## 11. Language-pack location in the component repo

- **Spec (§5):** `docs/_language-pack-{{language-pack}}.md` in
  the component repo. The blueprint stores the master pack at
  `language-packs/{{language-pack}}.md`.
- **Bootstrap-CLAUDE.md** (top-level, v2): Claude copies the
  chosen pack from `language-packs/<pack>.md` to
  `core/docs/_language-pack-<pack>.md` and then promotes
  `core/` to the repo root.
- **Open:** spec §10 OQ-2 (master-vs-component-local pack
  lifecycle) is unresolved. Skeleton treats post-bootstrap pack
  state as component-owned (spec §5 "Wer pflegt die
  Language-Packs" recommendation).

## 12. Multi-language representation in the skeleton

- **Spec (§6):** three multi-language modes (KMP-style,
  generator-emitted bindings, genuinely-coupled polyglot).
- **Skeleton:** shows the single-language case (default per
  §6). `BOOTSTRAP.md` notes how to extend (multiple
  `_language-pack-*.md` files + aggregation table in
  `CLAUDE.md`). No skeleton bloat for the rare cases.

---

## Items NOT raised here (tracked elsewhere)

- Sodium's own migration to the blueprint format — outside the
  Phase 2 scope per spec §9.
- CI provider choice (spec §10 OQ-4) — not blocking the
  blueprint v1.
- `.editorconfig` / `.gitattributes` / PR templates (spec §10
  OQ-5) — explicitly Phase 3.

---

# Feedback from first-use

This section is the catch-all for friction reports, surprises,
and convention gaps surfaced when teammates instantiate the
blueprint for a real component. The blueprint deliberately
skipped a synthetic dogfood run — the first teammate instantiation
is the validation pass. Phase B of the rollout plan is waiting
on entries here.

**Format for new entries** (append, do not edit prior items):

```
## YYYY-MM-DD — <reporter> — <component being bootstrapped>

**What stumbled:** one-paragraph description of where the
blueprint or BOOTSTRAP.md failed to be self-explanatory or
required improvisation.

**Workaround used:** what the reporter did to get past it.

**Suggested fix:** which file should change, and roughly how.
Mark `[for-spec]` if it requires a BLUEPRINT-SPEC.md change,
`[for-core]` if just `core/`, `[for-language-pack]` if pack-only.
```

_(no entries yet)_
