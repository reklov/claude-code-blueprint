<!--
  Step-001 seed — created at bootstrap from _template.md.
  Replace placeholders with the concrete first-step content.
  See BLUEPRINT-SPEC.md §4.5 and §7 (Bootstrap Process).

  At bootstrap time the file's name is renamed from
  `001-{{first-step-slug}}.md` to `001-<actual-slug>.md`. Update
  the link in PLAN.md when you rename.

  Placeholder notation:
    - {{FIRST-STEP-TITLE}}, {{first-step-slug}} — bootstrap-replaced.
    - <…> — pattern, filled by the author when populating this file.
-->

# Step 001 — {{FIRST-STEP-TITLE}}

**Status:** pending
**Linked ADRs:** ADR-001
**Branch:** _(not yet created)_

> **Historical names**
> - "Step 001" — current. (Genesis step; no renames.)

## Goal

<!-- One paragraph: what this step achieves and why it is the
     first thing the component does. -->
<one-paragraph-goal>

## Sub-steps

- _(none — single-cycle step)_  *(or list 001a, 001b, …)*

## Public API surface

<!-- The smallest viable public surface this step introduces. -->
```
<signatures-or-endpoint-list>
```

## TDD list — MANDATORY, written before any implementation

<!--
  Fill this in BEFORE creating the step branch. Each entry is a
  failing test that pins down a piece of the public surface above.
-->
- [ ] <test-1-description> — *(red → green → refactor)*
- [ ] <test-2-description>

## Smoke test

```
<smoke-command>
```

Expected outcome: <one-line-expectation>.

## Out of scope for this step

- <item> — deferred to Step <later-NNN>.

## Files touched

| File | Action | Reason |
|---|---|---|
| `<path/to/file.ext>` | new | <reason> |
