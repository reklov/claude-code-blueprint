<!--
  Skeleton for: <component-root>/docs/architecture.md
  Governed by:  BLUEPRINT-SPEC.md §4.7
  Purpose:      Canonical architecture document for the component.
                Module / package / crate / service breakdown,
                responsibilities, data flows, external interfaces.
                Diagrams encouraged (Mermaid renders inline on
                most platforms).
  Relationship to CLAUDE.md §"Architecture overview":
                CLAUDE.md gets the elevator pitch (a paragraph or
                two). This file gets the full picture. Do not
                duplicate; link.
  Update rule:  Same-commit updates per CLAUDE.md "Documentation
                freshness" — any code change that alters what is
                described here updates this file in the same
                commit.
-->

# {{COMPONENT-NAME}} — Architecture

## Overview

<!-- High-level summary in two or three paragraphs. Pointer to
     CLAUDE.md §"Architecture overview" for the elevator pitch. -->
<high-level-overview>

## Modules / packages

<!-- One subsection per top-level unit (module / crate / package /
     service). For each: responsibility in one sentence; the public
     surface it exposes; what it depends on. -->
### <module-name-1>

- **Responsibility:** <one-sentence>
- **Public surface:** <types-functions-or-endpoints>
- **Depends on:** <dependencies>

## Data flows

<!-- A diagram (Mermaid `sequenceDiagram` or `flowchart`) and / or
     bullet list per major flow. The point is to make the runtime
     story visible without reading code. -->
<diagram-or-bullet-list>

## External interfaces

<!--
  Which external systems this component talks to and how (HTTP,
  gRPC, message queue, filesystem). Refer to docs/api.md for
  the public surface this component exposes; refer here for
  external systems it consumes.

  **When to factor out into a dedicated doc.** Keep the entry
  here short — one line per external system, naming the
  interface and the rationale. If the description of a single
  external integration grows past roughly half a screen (live-
  discovered behaviour, deviations from the published spec,
  workarounds, version notes), factor it out into a dedicated
  `docs/external-<system>.md` file (see BLUEPRINT-SPEC.md §4.7).
  Link to it from the bullet here. Examples:
    docs/external-stripe.md   — payment-provider quirks
    docs/external-wire-api.md — third-party API deviations
-->
- <external-system-1> — <interface-and-rationale>

## Constraints

<!-- Hard architectural constraints that are decisions, not
     observations. Each one should have a corresponding ADR; cite
     it. -->
- <constraint-1> (see [`adrs/<NNN>-<slug>.md`](adrs/<NNN>-<slug>.md))
