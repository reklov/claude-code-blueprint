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
     section below. Three to six goals is typical; if you cannot
     name any, the component is trivial here — write that
     explicitly instead of leaving the section empty. -->
- **G1:** <security-goal-1, e.g. "Confidentiality of user credentials at rest and in transit">
- **G2:** <security-goal-2, e.g. "Integrity of audit log entries — append-only, tamper-evident">

## Non-goals (security)

<!-- Properties this component explicitly does *not* provide. Naming
     these prevents downstream consumers from assuming guarantees
     that aren't there. Examples: "we do not defend against an
     attacker who controls the host operating system", "we do not
     provide forward secrecy for archived messages". -->
- <non-goal-1>

## Trust boundaries

<!-- Each trust boundary as a separate subsection. For each:
     who is on each side, what crosses it, what is validated at
     the crossing. A trust boundary is wherever data or control
     passes between two parties whose assumptions about each
     other differ. -->
### <boundary-name-1, e.g. "Process ↔ disk">

- **Trusted side:** <component-or-role, e.g. "this process">
- **Untrusted side:** <component-or-role, e.g. "anyone with disk read access">
- **Crossing:** <data-or-control-flow, e.g. "serialized session tokens written to ~/.app/state.json">
- **Validation at crossing:** <checks-performed, e.g. "file mode 0600; contents encrypted with KEK from system keyring">

## Assets

<!--
  Concrete things this component holds or processes that have
  security value: keys, tokens, plaintext content, identifiers.
  For each: where it lives, who can see it, lifetime, zeroization
  policy if applicable.

  Severity ladder for prioritising work on this table:
    - **Critical**  — compromise breaks confidentiality of user
                      data or impersonation of another user.
    - **High**      — compromise affects integrity (silent
                      tampering, replay) without immediate
                      confidentiality loss.
    - **Medium**    — compromise enables denial-of-service or
                      information disclosure of metadata.
    - **Low**       — compromise leaks already-public or
                      derivable information.
  Tag each asset row with its severity in the Visibility column
  or in a separate Notes column.
-->
| Asset | Storage | Visibility | Lifetime | Zeroization |
|---|---|---|---|---|
| User session token | local secure storage (e.g. OS keychain) | this process only — Critical | until logout or expiry | wiped on logout; in-memory copies zeroed on drop |
| <asset-2> | <where> | <who — severity> | <when-disposed> | <policy> |

## Adversary model

<!--
  Who we defend against and what their capabilities are.
  Concrete and bounded. Generic "the attacker" is not enough.
  Pattern: name the adversary, list capabilities IN scope of
  defence, and list capabilities OUT of scope (those map to
  non-goals above).
-->
- **Network attacker:** can observe, drop, reorder, and inject
  TCP traffic between the component and its remote peers. Cannot
  break TLS with current ciphersuites. *Out of scope:* attackers
  who hold a CA-issued certificate for our domain.
- **<adversary-2>:** <capabilities-and-limits>

## Mitigations

<!-- The defenses currently in place. Each mitigation cites the
     code path or ADR that implements it. If a mitigation is
     planned but not yet implemented, list it under "Open issues"
     instead so this section stays factual. -->
- <mitigation-1> — see <adr-or-code-pointer>

## Open issues

<!-- Known security gaps or unresolved questions. Each one points
     to a Finding or to a future Step in PLAN.md. Triage by the
     severity ladder above. -->
- <open-item-1>
