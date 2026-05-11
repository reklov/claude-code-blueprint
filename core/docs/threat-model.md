<!--
  Skeleton for: <component-root>/docs/threat-model.md
  Governed by:  BLUEPRINT-SPEC.md §4.7
  Purpose:      Security-relevant assumptions. Trust boundaries,
                assets, adversary model, attack surface, mitigations.
                Required even when the component is non-cryptographic
                — if the component is genuinely trivial here, say
                so explicitly. Naming the trust boundaries is
                always valuable.
  Update rule:  Same-commit updates per CLAUDE.md "Documentation
                freshness". Any step that moves a trust boundary
                or introduces a new asset updates this file in
                the same commit. ADRs with security impact link
                back to the affected section.
-->

# {{COMPONENT-NAME}} — Threat Model

## Goals (security)

<!-- What security properties this component must provide. State
     them positively (G1, G2, …) and link each to the affected
     section below. -->
- **G1:** <security-goal-1>
- **G2:** <security-goal-2>

## Non-goals (security)

<!-- Properties this component explicitly does *not* provide. Naming
     these prevents downstream consumers from assuming guarantees
     that aren't there. -->
- <non-goal-1>

## Trust boundaries

<!-- Each trust boundary as a separate subsection. For each:
     who is on each side, what crosses it, what is validated at
     the crossing. -->
### <boundary-name-1>

- **Trusted side:** <component-or-role>
- **Untrusted side:** <component-or-role>
- **Crossing:** <data-or-control-flow>
- **Validation at crossing:** <checks-performed>

## Assets

<!-- Concrete things this component holds or processes that have
     security value: keys, tokens, plaintext content, identifiers.
     For each: where it lives, who can see it, lifetime, zeroization
     policy if applicable. -->
| Asset | Storage | Visibility | Lifetime | Zeroization |
|---|---|---|---|---|
| <asset-1> | <where> | <who> | <when-disposed> | <policy> |

## Adversary model

<!-- Who we defend against and what their capabilities are.
     Concrete and bounded. Generic "the attacker" is not enough. -->
- **<adversary-1>:** <capabilities-and-limits>

## Mitigations

<!-- The defenses currently in place. Each mitigation cites the
     code path or ADR that implements it. -->
- <mitigation-1> — see <adr-or-code-pointer>

## Open issues

<!-- Known security gaps or unresolved questions. Each one points
     to a Finding or to a future Step in PLAN.md. -->
- <open-item-1>
