<!--
  Skeleton for: <component-root>/reference/README.md
  Governed by:  BLUEPRINT-SPEC.md §4.8
  Purpose:      Explains the strict rules around the reference/
                directory and lists the submodules that have been
                pulled in, with the rationale for each.
-->

# {{COMPONENT-NAME}} — Reference material

This directory holds **read-only Git submodules** of foreign
repositories that this component consults for orientation.

## Strict rules

- **Submodules of external code only.** Component-specific spec
  artifacts (even those produced externally — for example a
  vendor-supplied OpenAPI file) belong in `docs/`, not here.
- **Read-only.** Never modify content under `reference/`. To
  update, pin the submodule to a different commit.
- **Removable without breaking the component.** No build-time or
  runtime code path may depend on `reference/`. It exists for
  humans and for Claude Code to read.

## Submodules

<!-- One entry per submodule: path, license, purpose, link. If
     none yet, replace this list with "None — this component has
     no external read-only references at present." -->
- _(none yet)_

## Why each submodule is here

<!-- For each submodule above: a sentence on what knowledge it
     provides that the component cannot get from its own code or
     from `docs/`. The point is to make every entry justified;
     unused references rot. -->
- _(populate when adding the first submodule)_
