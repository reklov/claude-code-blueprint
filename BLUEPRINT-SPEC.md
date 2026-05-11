# Blueprint Specification — Claude-Code-Repositories

**Status:** Draft v1.1
**Purpose:** Spezifikation der Datei- und Prozess-Struktur für alle künftigen Claude-Code-Repositories der Gruppe.
Aufbauend auf den gelernten Konventionen aus dem Sodium-Projekt.

---

## §1 Purpose & Non-Goals

**Was dieses Dokument ist:**
Eine verbindliche Spezifikation des **Core-Blueprints** für Komponenten-Repositories, die mit Claude Code entwickelt werden. Definiert Datei-Hierarchie, Sektions-Struktur jeder verbindlichen Datei, Update-Protokolle und das Interface, gegen das die sprachspezifischen Language-Packs (Rust, Go, TypeScript, Kotlin) implementiert werden.

**Was es nicht ist:**
- Keine Spezifikation der Language-Packs selbst (das sind separate Dokumente, ein Pack pro Sprache).
- Keine Tool-Auswahl auf Tool-Ebene (welcher Linter, welches Test-Framework — gehört in das jeweilige Pack).
- Keine Code-Style-Vorschriften (formatter-Output ist Code-Style, gehört auch ins Pack).
- Kein Repo-Management-Prozess (Branch-Policies, Review-Regeln — separate Doks der Org).

**Erwarteter Effekt:**
Jede neue Komponente startet mit identischem Skelett. Wer Komponente A kennt, kann Komponente B sofort lesen. Claude Code findet die gleichen Dateien an den gleichen Stellen mit der gleichen Sektions-Struktur und kann komponentenübergreifend gleichartig agieren.

---

## §2 Two-Layer Architecture

```
┌─────────────────────────────────────────────────┐
│           Component Repository                  │
│  (instantiiert aus Blueprint + Language-Pack)   │
└─────────────────────────────────────────────────┘
                      ▲
                      │ Bootstrap-Prozess
                      │
        ┌─────────────┴─────────────┐
        │                           │
┌───────────────┐         ┌─────────────────┐
│  Core         │         │  Language-Pack  │
│  Blueprint    │  ◄──── │  (rust / go /    │
│  (sprachfrei) │  uses   │  ts / kotlin)    │
└───────────────┘         └─────────────────┘
```

**Core-Blueprint** definiert:
- Datei-Hierarchie und Verzeichnis-Layout
- Sektions-Skelett aller Pflicht-Dateien
- Update-Protokolle und Rewrite-Verträge
- Sprachneutrales Vokabular für Build-/Test-Operationen (`pre-commit smoke`, `pre-merge gate`, …)
- ADR-Format
- Findings-Konvention
- Reference-Submodule-Konvention

**Language-Pack** definiert:
- Konkrete Befehle hinter dem sprachneutralen Vokabular (z.B. `pre-commit smoke = cargo fmt --check && cargo clippy && cargo test --lib`)
- Sprach-Idiome (Error-Handling, Module-Struktur, Test-Position)
- Build- und Manifest-Konventionen
- Bekannte Pitfalls und Workarounds der Sprache/Toolchain

**Component Repository** = Core-Blueprint + genau ein Language-Pack + komponenten-spezifische Inhalte.

---

## §3 Directory Layout

```
{{COMPONENT-NAME}}/
├── CLAUDE.md                          # Permanent conventions, hard rules
├── PLAN.md                            # Schlanke Status-Tabelle
├── README.md                          # Public-facing entry
├── FINDINGS.md                        # Index-Tabelle aller Findings (Status, Verweis)
├── docs/
│   ├── HANDOFF.md                     # Session-Übergabe (max. 150 Zeilen, rewrite-not-append)
│   ├── architecture.md                # Komponenten-Architektur, Diagramme, Datenflüsse
│   ├── threat-model.md                # Sicherheits-Annahmen, Trust Boundaries, Angriffsflächen
│   ├── api.md                         # Public-API-Manual (library / service / CLI / bindings)
│   ├── plan/
│   │   ├── README.md                  # Index + Format-Erklärung
│   │   ├── _template.md               # Schablone für neuen Step
│   │   └── 001-{{first-step-slug}}.md # Erster Step als gelebtes Beispiel
│   ├── adrs/
│   │   ├── README.md                  # Index + Format-Erklärung + Status-Lifecycle
│   │   ├── _template.md               # ADR-Schablone
│   │   └── 001-{{genesis-decision-slug}}.md
│   └── findings/
│       ├── README.md                  # Erklärt Format und Verhältnis zu FINDINGS.md
│       └── _template.md               # Finding-Schablone
├── reference/
│   └── README.md                      # Erklärt: hier nur read-only submodules
├── .claude/
│   └── settings.json                  # Geteilte Claude-Code-Config
└── .gitignore                         # inkl. .claude/settings.local.json
```

**Conventions im Layout:**

- `CLAUDE.md`, `PLAN.md`, `README.md`, `FINDINGS.md` liegen im Repo-Root in **Großschreibung**. Diese vier sind die "Eingangsschicht" — wer das Repo zum ersten Mal sieht, sieht sie sofort. Die Großschreibung ist visuelle Konvention, nicht Filesystem-Pflicht — aber konsistent durchgehalten.
- Alle Working-Dokumente (HANDOFF, plan-Details, ADRs, Finding-Volltexte) liegen unter `docs/`. Damit ist auf einen Blick sichtbar: Root = Permanent + Index, `docs/` = Working + Detail.
- Komponenten-spezifische Doks (`architecture.md`, `threat-model.md`, `api.md`) liegen in `docs/` und sind klein-geschrieben. Sie sind kein "Eingangs-Material", sondern Referenz, wenn man sie braucht.
- `reference/` ist explizit für Git-Submodule fremder Read-only-Repositories. Eigene Spec-Artefakte (z.B. eingecheckte OpenAPI-Spec eines Drittanbieters) gehören in `docs/`, nicht hierher — Begründung in `reference/README.md`.
- `_template.md` als Präfix kennzeichnet Schablonen, die **nicht** versioniert numerische Reihenfolge tragen und beim Erstellen eines neuen Items als Vorlage kopiert werden.
- **Numerische Präfixe** sind drei-stellig: ADRs als `001-…`, Steps als `001-…`, Findings als `001-…`. 999 Items pro Klasse reichen für jede Komponente, das vermeidet die `0001`-Bürokratie und die Auffüll-Sortier-Probleme bei zweistelliger Nummerierung.

**Placeholder-Konventionen — zwei Notationen, klar getrennt:**

1. **Bootstrap-Placeholder (`{{NAME}}`, mustache-style):** wird vom Bootstrap-Tool / -Prozess automatisch ersetzt. Bezeichnet komponenten-spezifische Werte, die einmal beim Anlegen der Komponente festgelegt werden und danach fix sind. Beispiele: `{{COMPONENT-NAME}}`, `{{OWNER}}`, `{{LANGUAGE-PACK}}`, `{{LICENSE}}`, `{{first-step-slug}}`.

2. **Pattern-Notation (`<NAME>`, angle-bracket-style):** wird **nicht** vom Bootstrap-Tool ersetzt und bleibt im fertig instantiierten Komponenten-Repo so stehen. Bezeichnet ein Schema, das von Entwicklern bei jeder Anwendung ausgefüllt wird. Beispiele: `step/<NNN>-<slug>` als Branch-Naming-Schema, `feat(step-<NNN>): …` als Commit-Prefix-Schema, `ADR-<NNN>` als Verweis-Notation.

Die Trennung ist mechanisch wichtig: ein Bootstrap-Tool, das blind alle `{{...}}` ersetzt, würde Pattern-Notation zerstören, wenn sie auch in Mustache stünde. Greppbarkeit beider Notationen bleibt erhalten — `{{` für Bootstrap-Stellen, `<NNN>` / `<slug>` etc. für Pattern.

**Faustregel zur Auswahl:** wenn der Wert beim Bootstrap einmal festgelegt wird und für die Lebenszeit der Komponente fest bleibt → Mustache. Wenn der Wert bei jeder Anwendung neu eingesetzt wird → Pattern.

---

## §4 Core Files — Detailed Specifications

### §4.1 `CLAUDE.md` (Repo-Root)

**Zweck:** Die permanente Konventionen-Quelle. Alles, was bei jeder Session gleich bleibt und nicht zur Tagesform gehört.

**Maximalgröße:** keine harte Grenze — das ist Referenzmaterial, nicht Lesefluss. Sodium liegt bei ~720 Zeilen, das ist OK. Was rein muss, muss rein.

**Pflicht-Sektionen (in dieser Reihenfolge):**

1. **`# {{COMPONENT-NAME}} — Conventions for Claude Code`** — H1 mit Komponentennamen.
2. **`## Reading order`** — In welcher Reihenfolge ein neuer Mitwirkender (Mensch oder Claude) die Doks zu lesen hat. Standard-Empfehlung: HANDOFF.md → CLAUDE.md → PLAN.md → docs/plan/<current-step-slug>.md → relevante ADRs.
3. **`## Hard rules`** — Non-negotiables. **Mindest-Set, das in jeder Komponente steht** (zusätzliche dürfen ergänzt werden):
   - *Strict TDD always.* Tests werden vor der Implementierung geschrieben und müssen rot sein, bevor Code geschrieben wird. Sprach- und Tooling-unabhängig. Bei Arbeit mit Claude Code unverhandelbar — die Test-Liste ist der Vertrag, gegen den die KI implementiert.
   - *English for committed content* (Code, Kommentare, Commit-Messages, Docs, ADRs, Plan-Files, Findings).
   - *Pre-commit smoke must pass before any commit* (Smoke-Definition kommt aus dem Language-Pack).
   - *Out-of-date documentation is a defect in review, not a follow-up task.*
   - *Documentation update happens in the same commit as the code change that necessitates it.*
   - *Tidyings stay separate from behavioural changes.* Ein Commit räumt auf (Renames, Extracts, Formatierung, Dead-Code-Entfernung — kein Verhaltenswechsel) oder ändert Verhalten — nie beides. Reviews bleiben so trennscharf und Reverts chirurgisch. (Kent Beck, *Tidy First?*)
   - *Accepted ADRs are not edited; supersession is the only forward path.*

   Maximal 10 Bullet-Punkte. Wenn mehr, ist es vermutlich keine Hard rule mehr, sondern Convention.
4. **`## User preferences`** — Wie die Hauptperson(en) der Komponente arbeiten möchten. Was Claude Code beachten soll: Sprache, Tool-Vorlieben, Stil. (Bei Schwarz Digits könnte ein Default-Block aus dem Org-Default geladen werden.)
5. **`## Architecture overview`** — Eine prägnante Architektur-Recap. Was die Komponente ist, wo sie hingehört, welche externen Abhängigkeiten sie hat. **Kein Step-Plan** (der ist in PLAN.md), **keine Entscheidungs-Begründung** (die ist in ADRs).
6. **`## Functional scope`** — Was ist drin, was ist explizit draußen. Diese Sektion ist die kanonische Quelle für In/Out-of-Scope. PLAN.md verweist hierhin, dupliziert nicht.
7. **`## Build, test, smoke — language-pack reference`** — Verweis auf das gewählte Language-Pack mit den konkreten Befehlen. Beispiel: *"This component uses the **Rust language-pack**. See `docs/_language-pack-rust.md` for `pre-commit smoke`, `pre-merge gate`, lint, and release-build commands."* Diese Sektion ist der Vertrag, der das Language-Pack ins Projekt einklinkt.
8. **`## Conventions`** — Sprachneutrale Konventionen, die für **diese** Komponente gelten und nicht im Language-Pack stehen. Datei-Naming, Test-Naming, Commit-Message-Format. Wenn rein sprachspezifisch, ins Pack verschieben.
9. **`## Documentation freshness`** — Die Regel: jede Code-Änderung mit user-visible-Effekt aktualisiert die zugehörige Doku **im selben Commit**. Kein Follow-up-Ticket.
10. **`## Update policy`** — Was darf in CLAUDE.md, was darf nicht, wie wird die Datei geändert. Kurz. Drei bis fünf Bullets.

**Was NICHT in CLAUDE.md gehört:**
- Tagesaktueller Status (→ PLAN.md)
- Step-Detail (→ docs/plan/)
- Architektur-Entscheidungs-Begründung (→ ADRs)
- Live-Discoveries (→ FINDINGS.md + docs/findings/)
- Session-Übergabe (→ HANDOFF.md)
- Komponenten-Architektur-Beschreibung jenseits eines kurzen Recaps (→ docs/architecture.md)

**Update-Protokoll:**
- Änderungen an Hard rules oder Functional scope erfordern einen ADR-Verweis im Commit.
- Sprachspezifische Inhalte werden NICHT in CLAUDE.md aufgenommen, sondern ins Language-Pack-Doc.

---

### §4.2 `PLAN.md` (Repo-Root)

**Zweck:** Die kanonische Status-Quelle. Wo stehen wir, was ist gemerged, was ist offen.

**Maximalgröße:** 180–240 Zeilen. **Wenn mehr, sind Inhalte am falschen Platz** — auslagern nach docs/plan/ (Step-Detail), ADRs (Entscheidungen) oder CLAUDE.md (Konventionen).

**Pflicht-Sektionen:**

1. **`# {{COMPONENT-NAME}} Implementation Plan`** + Last-update-Datum.
2. **Header-Absatz unmittelbar nach H1** — verweist auf CLAUDE.md (Konventionen), `docs/plan/` (Step-Prosa), `docs/adrs/` (Entscheidungen). Drei bis fünf Sätze. Macht klar: PLAN.md ist NUR Status.
3. **`## Status overview`** — die zentrale Tabelle. Spalten typischerweise: Step | Title | Status | Branch / PR | Notes. Status-Werte z.B.: `merged`, `in progress`, `pending`, `blocked`. **Diese Tabelle ist die einzige Wahrheit über den Step-Status der Komponente.**
4. **`### Cross-cutting commits on main`** — Bullet-Liste über Commits, die zu keinem einzelnen Step gehören (Tooling, Doku-Refactors, Dependency-Bumps).
5. **`### How to resume`** — Nummerierte Schritte für jemanden, der nach Pause weiterarbeitet. Typischerweise: lies HANDOFF, prüf Branch, lauf pre-commit smoke, schau in den aktuellen Step.
6. **`### Update protocol for this table`** — Wie und wann die Tabelle aktualisiert wird. Drei Bullets, nicht mehr.

**Was NICHT in PLAN.md gehört:**
- Architektur-Recaps (→ CLAUDE.md)
- TDD-Workflow-Beschreibungen (→ CLAUDE.md oder Language-Pack)
- "Suggested implementation order" für historische Steps (→ Git-History)
- Cross-cutting infrastructure decisions (→ ADRs)
- CLI- oder API-Specs (→ docs/library.md, docs/cli.md, docs/bindings.md)

**Update-Protokoll:**
- Bei jedem Step-Statuswechsel wird die Tabelle aktualisiert, im Commit, der den Statuswechsel verursacht.
- Bei Renumberings wird im historisch betroffenen `docs/plan/NN-*.md` ein "Historical names"-Block ergänzt, damit alte Verweise auflösbar bleiben.

---

### §4.3 `docs/HANDOFF.md`

**Zweck:** Session-Übergabe. Erste Datei, die Claude Code (oder ein wiedereinsteigender Mensch) liest. Beantwortet: "Was ist gerade Zustand und was tue ich jetzt?"

**Maximalgröße:** 150 Zeilen. **Wenn mehr, fault sie.** Die Größenbeschränkung ist nicht ästhetisch — sie zwingt zum kompletten Rewrite, und der komplette Rewrite zwingt zur Aktualität.

**Pflicht-Sektionen:**

1. **`# Handoff — {{COMPONENT-NAME}}`** + aktuelles Datum + Branch.
2. **`## §0 First action`** — die Befehle, die der Wiedereinsteiger als erstes auszuführen hat (typischerweise `git status`, `pre-commit smoke`), und der erwartete Output (z.B. "194 lib tests green"). Maximal 30 Zeilen. Konkret, keine Prosa.
3. **`## §1 Brief from previous session`** — was wurde zuletzt gemerged, was ist seitdem passiert. Maximal 25 Zeilen. Keine Erklärung *warum*, das steht in ADRs oder Step-Files.
4. **`## §2 Concrete first task`** — was steht als nächstes an. Verweis auf `docs/plan/NN-*.md` für Detail. Maximal 40 Zeilen.
5. **`## §3 Quick reference`** — Branch, aktueller Test-Count, letzte Finding-Nummer, nächste TODO. Maximal 15 Zeilen.
6. **`## §4 Update contract`** — der explizite Vertrag, kurz: *"Rewrite this file completely at every session end. Do not append. If it grows past 150 lines, split or delete sections — do not let it become a changelog."*

**Was NICHT in HANDOFF.md gehört:**
- Reading order, Hard rules, User preferences, Error handling (→ CLAUDE.md, Verweis genügt)
- Ausführliche Step-Liste (→ PLAN.md Status-Tabelle, Verweis genügt)
- Wire-API-Notes, Threat-Model, Domain-spezifische Pitfalls (→ jeweilige docs/-Datei, Verweis genügt)

**Update-Protokoll (kritisch):**
- **Rewrite, not append.** Bei jedem Session-Ende wird die Datei komplett neu geschrieben, nicht ergänzt.
- Wenn beim Schreiben die 150-Zeilen-Grenze überschritten wird, ist das ein Defekt-Signal: irgendwas wandert in eine andere Datei (typischerweise: zu viel Kontext aus §1 → ist Step-Detail → gehört in `docs/plan/NN-*.md`).
- Wenn du nicht weißt, was die letzte Session gemacht hat, **lies Git-Log statt zu raten**. HANDOFF.md raten ist genau, wie sie verrottet.

---

### §4.4 `docs/adrs/` — Architecture Decision Records

**Zweck:** Jede Entscheidung, die später jemand in Frage stellen wird, wird hier festgehalten — mit den damals erwogenen Alternativen und der Begründung.

**Datei-Naming:** `NNN-kurz-beschreibender-name.md`, drei-stellige Nummer, lückenlos durchgezählt. (999 ADRs reichen für jede realistische Komponenten-Lebensdauer.)

**Pflicht-Sektionen jedes ADR:**

```markdown
# ADR-<NNN>: <decision title in imperative form>

**Status:** <Proposed | Accepted | Superseded by ADR-<NNN> | Deprecated>
**Date:** YYYY-MM-DD
**Deciders:** <name(s)>

---

## Context
Was ist die Situation, was ist das Problem, welche Constraints gelten.

## Options
### Option A: <name>
- Beschreibung
- Pro
- Con

### Option B: <name>
… analog …

## Decision
**We <decision in imperative>.**

Begründung: welche Faktoren waren entscheidend.

## Consequences
### Positive
- …
### Negative
- …
### Neutral
- …

## Implementation Notes
Konkrete Hinweise für die Umsetzung — welche Files betroffen, welche Commits, welcher Step im PLAN.

## References
- ADR-NNN (vorhergehende verwandte Entscheidung)
- Externe Dokumente
- Spezifikationen

## Revision History
- YYYY-MM-DD: Created
- YYYY-MM-DD: Status changed to Accepted
```

**Status-Lifecycle:**
- `Proposed` → `Accepted` → ggf. `Superseded by ADR-NNN` (mit Verweis auf nachfolgendes ADR) oder `Deprecated`.
- **Akzeptierte ADRs werden inhaltlich nicht mehr verändert.** Wenn die Welt sich ändert, schreibst du ein neues ADR, das das alte supersedes — du editierst nicht das alte um. (Ausnahme: typo-Korrekturen, Link-Reparaturen.)

**ADR-001 ist der "Genesis"-ADR:** dokumentiert die initiale Architektur-Wahl der Komponente. Welche Sprache, welche Hauptdependencies, welcher übergeordnete Architektur-Stil. Wird beim Bootstrap angelegt.

**`docs/adrs/README.md`** enthält den Index aller ADRs als Tabelle (Nr | Titel | Status | Datum) und eine knappe Erklärung des Formats und Lifecycles.

**`docs/adrs/_template.md`** ist die kopierbare Schablone für neue ADRs.

---

### §4.5 `docs/plan/` — Per-Step Detail Files

**Zweck:** Die Prosa-Detail-Dokumentation pro Implementierungs-Step, die in PLAN.md keinen Platz mehr findet (und nicht finden soll).

**Datei-Naming:** `NNN-kurz-beschreibender-name.md`, drei-stellige Nummer, korrespondiert zur Step-Nummer in der PLAN.md-Status-Tabelle.

**Empfohlene Sektions-Struktur eines Step-Files:**

```markdown
# Step <NNN> — <Title>

**Status:** <merged | in progress | pending | blocked>
**Linked ADRs:** ADR-<NNN>, …
**Branch:** <branch-name> *(falls in progress)*

> **Historical names** *(falls renumbered)*:
> - "Step <XXX>" — vor YYYY-MM-DD
> - "Step <NNN>" — seit YYYY-MM-DD, current

## Goal
Ein Absatz: was wird mit diesem Step erreicht.

## Sub-steps
- <NNN>a: <kurzer-sub-step>
- <NNN>b: <…>

## Public API surface
Welche neuen öffentlichen Funktionen/Typen entstehen, welche Signaturen.

## TDD list — MANDATORY, written before any implementation
Geordnete Test-Liste. Diese Liste **muss vor dem ersten Implementierungs-Commit existieren**, und jeder Test wird **rot** geschrieben, bevor sein Implementierungs-Code geschrieben wird. Die Reihenfolge in der Liste = die Reihenfolge der Commits.

Format:
- [ ] <test-1-description> — *(red → green → refactor)*
- [ ] <test-2-description>
- …

Beim Step-Abschluss sind alle Boxen abgehakt und im Code grün.

## Smoke test
Konkreter Befehl, mit dem das End-to-End-Funktionieren validiert wird. Verweist typischerweise auf das Language-Pack-Vokabular (`pre-merge gate`).

## Out of scope for this step
Was bewusst nicht in diesem Step landet, mit Verweis auf späteren Step.

## Files touched
Tabelle der zu erstellenden / zu ändernden Dateien.
```

**`docs/plan/README.md`** enthält den Index der Step-Files (typischerweise auch im PLAN.md-Header verlinkt) und das Format-Schema.

**`docs/plan/_template.md`** ist die kopierbare Schablone für neue Steps.

**Update-Protokoll:**
- Bei Renumbering: Datei wird über `git mv` umbenannt, der "Historical names"-Block oben in der Datei wird erweitert.
- Bei Status-Wechsel: Status-Zeile oben anpassen, **gleicher Commit, der den Status auch in PLAN.md aktualisiert**.
- Die TDD-Liste wird **nicht nachträglich angepasst, um zur Implementierung zu passen**. Wenn ein zusätzlicher Testfall beim Schreiben der Implementierung auffällt, wird er rot eingecheckt, bevor er grün gemacht wird.

---

### §4.6 `FINDINGS.md` (Repo-Root) + `docs/findings/`

**Zweck:** Live-Discoveries — Dinge, die du beim Smoke-Testen oder Live-Debugging entdeckst und die anderen Mitwirkenden Zeit sparen, wenn sie sie kennen.

**Struktur (Index + Volltext, analog zu ADRs):**

`FINDINGS.md` im Repo-Root ist eine **Index-Tabelle**. Volltext jedes Findings liegt als eigene Datei `docs/findings/<NNN>-<slug>.md`. Diese Trennung skaliert über die Lebensdauer einer Komponente — eine append-only-Einzeldatei wäre nach 100 Findings unlesbar.

#### `FINDINGS.md` (Index)

```markdown
# Findings — {{COMPONENT-NAME}}

Index of live-discovered behaviour, gotchas, and surprises.
Full content per finding under `docs/findings/`.

| Nr | Title | Date | Status | Tags | Link |
|----|---|---|---|---|---|
| 001 | <kurzer-titel> | YYYY-MM-DD | open | smoke,external-api | [→](docs/findings/001-<slug>.md) |
| 002 | … | … | understood | … | [→](docs/findings/002-….md) |
| 003 | … | … | superseded by §005 | … | [→](docs/findings/003-….md) |
```

**Status-Werte:**
- `open` — entdeckt, noch keine Erklärung oder Workaround.
- `understood` — Erklärung gefunden, ggf. Workaround dokumentiert. Standardzustand für die meisten Findings.
- `superseded by §NNN` — neueres Finding ersetzt dieses (z.B. weil sich die externe Welt geändert hat oder wir die Sache jetzt besser verstehen).
- `obsolete` — die Bedingung gilt nicht mehr (externes System hat sich geändert, eigener Code hat das Problem umgangen).

**Update-Protokoll Index:**
- Bei jedem neuen Finding wird genau eine Zeile angefügt.
- Status-Änderungen werden in der Tabelle direkt editiert. Die zugehörige Volltext-Datei wird mit einer "Revision History"-Zeile versehen.
- Numerierung ist immutable. Auch obsolete oder superseded-Findings behalten ihre Nummer.

#### `docs/findings/<NNN>-<slug>.md` (Volltext)

```markdown
# Finding §<NNN> — <Title>

**Date:** YYYY-MM-DD
**Status:** <open | understood | superseded by §<NNN> | obsolete>
**Tags:** <kommagetrennt, z.B. smoke, external-api, race-condition>
**Discovered during:** <Step <NNN> smoke / Live debugging / Code review / …>

## Observation
Was genau wurde beobachtet. Konkret, mit Befehlsausgabe / Log-Auszug / Stacktrace, wenn möglich.

## Investigation
Was wurde getan, um das zu verstehen.

## Explanation
Warum tritt das auf.

## Implication
Was bedeutet das für unsere Implementierung. Konkrete Konsequenz.

## Workaround / Fix
Wie wird damit umgegangen. Verweis auf Commit / ADR / Step-Änderung.

## References
- Commit-SHAs
- ADRs
- Steps
- Externe Issues / Specs
```

**Verhältnis zu ADRs:**
Findings sind keine Architektur-Entscheidungen. Wenn aus einem Finding eine Entscheidung resultiert (z.B. Workaround-Strategie hat strategische Tragweite), wird ein ADR geschrieben, der das Finding referenziert. Das Finding bleibt der "Beobachtungs-Anker", der ADR ist die "Entscheidungs-Spur".

**`docs/findings/README.md`** erklärt das Format und die Beziehung zwischen Index und Volltext.

**`docs/findings/_template.md`** ist die kopierbare Schablone für neue Findings.

---

### §4.7 Komponenten-spezifische Docs in `docs/`

**Zweck:** Die fachlich-technische Beschreibung der Komponente selbst — getrennt von Konventionen, Status, und Entscheidungen.

**Pflicht-Files (im Blueprint als Skeleton vorhanden):**

- **`docs/architecture.md`** — Die Architektur der Komponente. Module/Pakete/Crates/Services und ihre Verantwortlichkeiten. Datenflüsse zwischen den Teilen. Externe Schnittstellen (welche Systeme reden mit dieser Komponente und wie). Diagramme, wo sie helfen — Mermaid bevorzugt, weil im Repo gerendert. **Diese Datei ist umfangreicher als die Architecture-Recap-Sektion in CLAUDE.md** und ist die kanonische Quelle für "wie ist das gebaut".
- **`docs/threat-model.md`** — Sicherheits-relevante Annahmen. Trust Boundaries, Assets, Adversary Model, Angriffsflächen, Mitigationen. Auch wenn die Komponente keine kryptographische ist: jedes System hat Trust Boundaries, und sie zu benennen ist wertvoll. Wenn die Komponente trivial ist (z.B. ein reines Validation-Tool), darf die Datei kurz sein und das explizit feststellen.
- **`docs/api.md`** — Die öffentliche API der Komponente. Bei einer Library: Funktionssignaturen und Verwendungs-Beispiele. Bei einem Service: Endpoints, Request/Response-Schemata, Auth. Bei einem CLI-Tool: Commands, Flags, Exit Codes. Bei einer Komponente mit mehreren Schnittstellen-Typen: Sektionen pro Typ in einer Datei, oder Aufsplitten in `docs/api-library.md`, `docs/api-cli.md`, `docs/api-bindings.md`.

**Optionale Files** (werden angelegt, wenn die Komponente sie braucht — keine im Skeleton):

- `docs/operations.md` — Deployment, Monitoring, Runbooks, Incident Response (für Services).
- `docs/protocol.md` — Protokoll-Spezifikation (für Komponenten, die ein Wire-Protokoll definieren).
- `docs/migrations.md` — Daten- oder Schema-Migrationen.
- `docs/external-<system>.md` — Notizen zu fremden Systemen, mit denen die Komponente integriert (z.B. Sodiums `WIRE-API-NOTES.md`).
- `docs/glossary.md` — Wenn die Komponente in einer Domäne mit eigenem Vokabular operiert.

**Update-Protokoll:**
- `architecture.md` und `api.md` werden im selben Commit aktualisiert wie Code-Änderungen, die ihre Aussagen verändern (gemäß `documentation freshness`-Hard-Rule).
- `threat-model.md` wird bei jedem Step aktualisiert, der Trust Boundaries verschiebt oder neue Assets einführt. Bei jedem ADR mit Sicherheits-Auswirkungen wird im ADR auf die zugehörige threat-model.md-Sektion verwiesen.



### §4.8 `reference/`

**Zweck:** Read-only Git-Submodule fremder Repositories, die zur Orientierung konsultiert werden.

**Strenge Regeln:**
- **Nur Submodule fremder Codebasen.** Eigene Spec-Artefakte (auch wenn extern erzeugt, z.B. von einem Drittanbieter eingecheckte OpenAPI-Datei) gehören nach `docs/`.
- **Read-only.** Kein Modifizieren von Inhalten unter `reference/`. Wenn sich der Stand ändern soll, wird das Submodule auf einen anderen Commit gepinnt.
- **Entfernbar ohne Repo-Bruch.** Es darf kein Code-Pfad existieren, der `reference/` zur Build-Zeit oder Laufzeit benötigt. `reference/` ist ausschließlich Lese-Material für Menschen und Claude Code.

**`reference/README.md`** erklärt diese Regeln plus Liste der eingebundenen Submodule mit Begründung, warum jedes davon gebraucht wird.

---

### §4.9 `.claude/`

**Zweck:** Claude-Code-Konfiguration für die Komponente.

**Konvention:**
- `.claude/settings.json` ist eingecheckt und teilt komponente-weite Settings mit allen Mitwirkenden (Permissions, MCP-Server-Listen, Tool-Whitelists).
- `.claude/settings.local.json` ist lokal pro Benutzer und in `.gitignore`.
- `.claude/commands/` und `.claude/agents/`, falls vorhanden und sinnvoll fürs Team, werden eingecheckt.

**`.gitignore`-Eintrag (Pflicht):**
```
.claude/settings.local.json
```

---

## §5 Language-Pack Interface

Ein Language-Pack ist ein Markdown-Dokument (`docs/_language-pack-{{language-pack}}.md` im Komponenten-Repo, oder zentral im Blueprint-Repo gepflegt und beim Bootstrap kopiert), das die folgenden Sektionen liefert. **Diese Sektionen sind das Interface — fehlt eine, ist das Pack unvollständig.**

### Pflicht-Sektionen jedes Language-Packs

1. **`# Language-Pack: <Language>`** — H1.

2. **`## Build manifest`** — Welcher Manifest-Datei (Cargo.toml / go.mod / package.json / build.gradle.kts), welche Mindestversionen, welche Edition/Standard.

3. **`## Module / package layout`** — Sprachspezifische Struktur. Wo liegen Sources, wo Tests, wo Docs.

4. **`## Test position convention`** — Wo gehören Unit-Tests hin (inline im Source / separates File / separates Verzeichnis), wo Integration-Tests, wo Doc-Tests / Examples.

5. **`## Sprachneutrale Operationen → konkrete Befehle`** — eine Tabelle:

   | Sprachneutrale Operation | Konkreter Befehl |
   |---|---|
   | `pre-commit smoke` | `<format-check> && <lint> && <unit-tests>` |
   | `pre-merge gate` | `pre-commit smoke && <integration-tests> && <coverage-check>` |
   | `lint` | `<lint-command>` |
   | `format` | `<format-command>` |
   | `format-check` | `<format-check-command>` |
   | `unit-tests` | `<unit-test-command>` |
   | `integration-tests` | `<integration-test-command>` |
   | `dep-license-check` | `<license-check-command>` |
   | `api-docs-generate` | `<doc-gen-command>` |
   | `release-build` | `<release-build-command>` |

6. **`## Error-handling idiom`** — Welches Pattern Standard ist (Result/error/exception/sealed). Beispiele für korrekten Umgang.

7. **`## Common pitfalls`** — Bekannte Fußangeln der Sprache/Toolchain. Z.B. bei Rust: edition-2024 vs `#[no_mangle]`. Bei Go: nil-channel-deadlocks, goroutine-leaks. Bei TS: `any`-Vermeidung, strikte `tsconfig`-Settings. Bei Kotlin: KMP `expect/actual`-Synchronisation.

8. **`## Recommended dependencies`** — Quasi-Standard-Bibliotheken, die in dieser Sprache für typische Aufgaben (Logging, Tests, Serialization, HTTP) bevorzugt werden. Begründung für jede Empfehlung.

9. **`## Release & versioning`** — SemVer-Anwendung in dieser Sprache, Tag-Konvention, Release-Artefakte.

### Optional, aber empfohlen

- **`## Cross-compilation targets`** — falls für die Sprache relevant (Rust: aarch64/iOS/wasm32; Kotlin KMP: jvm/native/js).
- **`## IDE / editor setup`** — empfohlene Plugins, settings.

### Wer pflegt die Language-Packs?

Empfehlung: ein zentrales `claude-code-blueprint`-Repo enthält die Master-Versionen aller Language-Packs. Beim Bootstrap einer neuen Komponente wird das passende Pack reinkopiert. Updates an den Master-Packs werden über einen kontrollierten Prozess in bestehende Komponenten zurückportiert (oder explizit nicht — Komponente kann ihren Pack-Stand pinnen).

---

## §6 Multi-Language Components

**Default-Regel:** Eine Komponente, eine Sprache, ein Language-Pack-Verweis. Splitting entlang Sprach-Grenzen wird der Polyglot-Repo-Variante vorgezogen — schon weil CI-Pipelines, Dependency-Management und Tool-Setup pro Sprache deutlich einfacher sind.

In drei Konstellationen weicht man von der Default-Regel ab. Die Entscheidung *welche* Konstellation vorliegt, gehört in den Genesis-ADR (ADR-001) der Komponente.

### §6.1 Plattform-Varianten derselben Sprache (KMP-Stil)

Beispiel: Kotlin Multiplatform mit `commonMain` (Kotlin), `iosMain` (Kotlin + minimaler Swift-Glue), `androidMain` (Kotlin), `jvmMain` (Kotlin).

**Behandlung:** Eine Sprache, ein Language-Pack (`kotlin-kmp.md`). Der Pack hat eine Sektion "Plattform-Varianten", die `expect/actual`-Konventionen, Plattform-spezifische Test-Befehle und gegebenenfalls Glue-Sprachen-Hinweise abdeckt. Native-Glue-Code (Swift im iOS-Target, Java-Interop) zählt zur Plattform, nicht zu einer eigenen Sprache.

### §6.2 Generator-emittierte Bindings (Sodium-Stil)

Beispiel: Rust-Core mit UniFFI-generierten Bindings für Kotlin, Swift, TypeScript-WASM. Die Bindings sind Build-Artefakte, kein Source-Code.

**Behandlung:** Eine Sprache (Rust), ein Language-Pack (`rust.md`). Der Pack hat eine Sektion "Bindings & cross-compilation", die Generierung, Cross-Compile-Targets und das Test-Setup für die Bindings (typischerweise eigenes Test-Harness pro Ziel-Sprache, das gegen die generierten Artefakte läuft) beschreibt. Die Ziel-Sprachen tauchen nicht als Language-Packs auf, weil niemand sie händisch schreibt.

### §6.3 Genuin gekoppelter polyglotter Code (selten)

Beispiel: Web-Komponente mit Rust-zu-WASM-Modul + handgeschriebenem TypeScript-Wrapper im selben Repo, beide gleich gewichtet. Oder: Service mit Go-Backend und TypeScript-Frontend, deren API-Verträge eng zusammen evolvieren und die deshalb nicht in separate Repos sollen.

**Behandlung:** Mehrere Language-Packs in einer Komponente. Konkrete Regeln:

- Eine Sprache wird als **primary language** designiert (im Genesis-ADR begründet, in CLAUDE.md genannt). Sie definiert das Top-Level-Vokabular.
- Sub-Areas in distinct Verzeichnissen, jedes mit eigener Sprach-Zuordnung. Konvention: Top-Level-Verzeichnis-Name = Sub-Area-Name. Beispiel: `apps/web/` (TypeScript), `services/api/` (Go).
- **Pro Sub-Area** ein Language-Pack-Doc: `docs/_language-pack-{{primary-language-pack}}.md`, `docs/_language-pack-{{secondary-language-pack}}.md`. Jeder Pack ist sich selbst gegenüber komplett (alle Pflicht-Sektionen aus §5).
- **Top-Level-Operationen aggregieren.** `pre-commit smoke` der Komponente = Sub-Area-spezifische Smoke-Befehle in einer definierten Reihenfolge. Die Aggregation steht in CLAUDE.md §"Build, test, smoke" und ist der einzige Ort, wo Komponenten-übergreifend orchestriert wird.
- Beispiel-Tabelle in CLAUDE.md:

  | Operation | Befehl |
  |---|---|
  | `pre-commit smoke` | `(cd services/api && go test ./... && golangci-lint run) && (cd apps/web && npm run lint && npm test)` |
  | `pre-merge gate` | `pre-commit smoke && integration-tests` *(eigenes Top-Level-Verzeichnis `tests/integration/`)* |

- **Strict TDD gilt pro Sub-Area mit der dortigen Sprach-Toolchain.** Step-Files können Tests in beiden Sprachen referenzieren (z.B. ein API-Vertrag wird im Go-Test und im TS-Test geprüft).

### §6.4 Wann eine Komponente gesplittet gehört

Symptome, die für Split sprechen:

- Sub-Areas haben unabhängige Release-Zyklen.
- Sub-Areas haben unterschiedliche Owner-Teams.
- Sub-Areas haben unabhängige Deployment-Targets (Frontend zu CDN, Backend zu Kubernetes — und keiner zwingt sie, gemeinsam zu releasen).
- Die Kopplung zwischen den Sprachen passiert über eine stabile, versionierte Schnittstelle (REST-API, gRPC, Protobuf-Schema), nicht über Code-Sharing.

Wenn alle vier zutreffen: separate Komponenten, separate Repos. Die Kopplungs-Schnittstelle (z.B. das Protobuf-Schema) lebt entweder im Backend-Repo (kanonische Quelle) oder in einem dritten Schema-Repo, und das Frontend zieht es über `reference/` ein.

---

## §7 Bootstrap Process

Der Bootstrap ist **Claude-Code-driven**. Der Blueprint-Repo enthält am Top-Level eine `CLAUDE.md` im "Bootstrap-Modus", die Claude beim ersten Start liest und als Anweisung ausführt. Es gibt keinen separaten Bootstrap-Befehl, kein Init-Skript und keine manuelle Checkliste — der Bootstrap-Pfad ist die Architektur selbst.

### §7.1 Was der User tut

```bash
git clone <blueprint-remote>/claude-code-blueprint <your-component>
cd <your-component>
claude
```

Falls Claude beim Start nicht von selbst in den Bootstrap-Modus geht, prompt explicit: *"Read CLAUDE.md and start the bootstrap."*

### §7.2 Was Claude im Bootstrap-Modus tut

Schritt für Schritt:

1. **Sprach-Auswahl.** Erste Frage in Englisch: in welcher Sprache soll die Bootstrap-Konversation laufen? Antwort des Users bestimmt die Chat-Sprache. **Committed content bleibt Englisch** per Hard Rule — nur das Prompt-Gespräch wechselt.

2. **Bootstrap-Fragen** in der gewählten Chat-Sprache:
   - `COMPONENT-NAME` (kebab-case, z.B. `civicseal-attestation`)
   - `COMPONENT-TITLE` (lesbar, z.B. "CivicSeal Attestation Service")
   - `OWNER` (Person oder Team)
   - `LANGUAGE-PACK` (`rust` | `go` | ...) — bei polyglotten Komponenten zwei separate Antworten: `PRIMARY-LANGUAGE-PACK` plus eine Liste `SECONDARY-LANGUAGE-PACK[]` (siehe §6.3)
   - `LICENSE` (SPDX-Identifier, z.B. `Apache-2.0`; oder `LicenseRef-…` für interne Lizenzen)
   - `INITIAL-GENESIS-DECISION` (Kurztitel des ersten ADR — typischerweise die Sprach-/Architektur-Wahl selbst, bei polyglotten Komponenten zusätzlich die Multi-Language-Begründung gemäß §6)
   - `FIRST-STEP-TITLE` (Titel von Step 001)

   Aus `INITIAL-GENESIS-DECISION` und `FIRST-STEP-TITLE` leitet Claude jeweils einen kebab-case-Slug ab und bestätigt mit dem User.

3. **Spec lesen.** Claude liest `BLUEPRINT-SPEC.md` §3 + §7 + die Sektions-Schemata aller berührten Files (§4.x). Bei Konflikt zwischen den Anweisungen in der Top-Level `CLAUDE.md` und dem Spec gewinnt der Spec — Diskrepanz wird dem User gemeldet.

4. **Plan vorstellen.** Bevor irgendetwas verändert wird, präsentiert Claude:
   - Mustache-Substitutionen (Tabelle: `{{...}}` → konkrete Werte). Angle-Bracket-Pattern `<...>` bleiben unverändert per §3.
   - Datei-Renames der Seed-Files (`001-{{first-step-slug}}.md`, `001-{{genesis-decision-slug}}.md`).
   - Datei-Move des gewählten Language-Packs (`language-packs/<pack>.md` → `core/docs/_language-pack-<pack>.md`).
   - Datei-Löschungen der blueprint-only Artefakte: `BLUEPRINT-SPEC.md`, `OPEN-QUESTIONS.md`, `language-packs/` (Verzeichnis), `core/` (nach Promote zum Root), Top-Level `README.md`, Top-Level `CLAUDE.md` (dieses File selbst).
   - Git-Operationen: `rm -rf .git/` (Blueprint-History wegwerfen), `git init -b main`, `git add . && git commit -m "chore: bootstrap component from blueprint"`.

   Claude wartet auf explizite Bestätigung des Users. Änderungswünsche werden eingearbeitet, dann erneut bestätigt.

5. **Ausführen.** Nach Bestätigung führt Claude die Operationen aus:
   - Replace aller `{{...}}` Mustaches via literal-string-Substitution. `<...>` Pattern bleiben unangetastet.
   - Rename der zwei Seed-Files; Cross-References (PLAN.md status-Zeile, Links innerhalb der Seed-Files) werden im selben Pass aktualisiert.
   - Copy des gewählten Language-Packs nach `core/docs/_language-pack-<pack>.md`.
   - Promote `core/*` zum Repo-Root.
   - Löschung der blueprint-only Artefakte.
   - Drop des `.git/` Verzeichnisses (war vom Blueprint-Clone).
   - `git init -b main`.
   - First commit: `chore: bootstrap component from blueprint`.

   Am Ende läuft eine Verifikation (alle Mandatory-Files vorhanden, drei-stellige Numerierung, keine unsubstituierten `{{...}}` außer den literal-`{{…}}`-Beispiel-Strings in `_template.md` HTML-Kommentaren).

6. **Handoff.** Claude teilt dem User mit: Bootstrap fertig, **Claude Code bitte neustarten**, damit die echte component-`CLAUDE.md` (vormals `core/CLAUDE.md`) bei der nächsten Session als Anweisungs-Quelle gelesen wird. Erste echte Aufgabe: `docs/HANDOFF.md` + `docs/plan/001-<first-step-slug>.md`.

### §7.3 Failure recovery

Wenn der Bootstrap mittendrin scheitert (Claude-Crash, User-Abbruch, eine destruktive Operation überrascht den User), ist der Reset:

```bash
cd ..
rm -rf <your-component>
git clone <blueprint-remote>/claude-code-blueprint <your-component>
cd <your-component>
claude
```

Der Blueprint-Remote ist unverändert; nur das partielle Komponenten-Verzeichnis wird verworfen. Idempotente Re-Runs sind explizit *nicht* Ziel — der Reset-Pfad ist günstiger zu warten.

### §7.4 Manueller Fallback

**Es gibt keinen.** Wenn Claude Code nicht verfügbar ist, macht ein Claude-Code-Projekt ohnehin keinen Sinn — die ganze Architektur (TDD-Liste als Vertrag, Konventionen für KI-Lesbarkeit, Step-Files als kanonischer Plan) ist auf Claude-Code-Nutzung ausgelegt.

---

## §8 Maintenance Contracts (Meta-Rules)

Die Regeln, die alle Files gemeinsam tragen:

1. **Strict TDD always.** Tests vor Code, rot vor grün. Kein "ich schreib die Tests danach noch dazu". Bei Arbeit mit Claude Code unverhandelbar — die Test-Liste im Step-File ist der Vertrag, gegen den die KI implementiert. Ohne diesen Vertrag fehlt der Korrektheits-Anker, und Claude Code kann auf scheinbar plausible aber inkorrekte Implementierungen abdriften.
2. **Out-of-date documentation is a defect.** Wird im Review wie ein Bug behandelt, nicht wie ein Follow-up.
3. **Single source of truth pro Inhaltstyp.** Status → PLAN.md. Konventionen → CLAUDE.md. Entscheidungen → ADRs. Step-Detail → docs/plan/. Findings-Index → FINDINGS.md, Findings-Volltext → docs/findings/. Architektur-Beschreibung → docs/architecture.md. Session-Übergabe → HANDOFF.md. Doppelte Pflege wird vermieden.
4. **Rewrite over append, except FINDINGS-Index.** HANDOFF.md, PLAN.md werden bei Veränderung neu geschrieben (in ihrer schlanken Form), nicht ergänzt. Findings-Index wächst monoton (eine neue Tabellenzeile pro Finding), Findings-Volltext-Files sind individuell editierbar (Status-Änderungen, neue References).
5. **References, not duplication.** Wenn ein Inhalt schon in einer kanonischen Datei steht, wird in anderen Dateien verwiesen, nicht kopiert.
6. **Hard caps make rewrites enforceable.** HANDOFF.md ≤ 150 Zeilen, PLAN.md ≤ 240 Zeilen. Wer drüber kommt, behebt das Symptom (Inhalt am falschen Platz), nicht den Cap.
7. **English for committed content.** Code, Kommentare, Commit-Messages, Doku, ADRs, Plan-Files, Findings — Englisch. Persönliche Notizen außerhalb des Repos sind frei.
8. **Same-commit doc updates.** Code-Änderung, die user-visible-Verhalten ändert, aktualisiert die zugehörige Doku im selben Commit.
9. **Accepted ADRs are immutable.** Welt ändert sich → neuer ADR, der den alten supersedes. Editieren der akzeptierten Decision selbst ist nicht zulässig.
10. **Numbering is immutable.** Einmal vergebene Nummern (ADRs, Steps, Findings) werden nicht recyclet. Auch obsolete Items behalten ihre Nummer.

---

## §9 Phase 2 Briefing — Was Claude Code mit dem Sodium-Repo tut

Sobald dieser Spec-Stand abgestimmt ist, übergeben wir an Claude Code mit dem folgenden Auftrag:

**Input:**
- Diese Datei (`BLUEPRINT-SPEC.md`).
- Read-Zugriff auf das Sodium-Repo (lokales Clone oder GitHub).

**Output:**
- Ein neues Repo `claude-code-blueprint/` mit:
  - `core/` — alle Pflicht-Files aus §4 als Skeleton mit Sektions-Headings, HTML-Kommentaren als Leitfaden, und `{{...}}`-Placeholdern an den richtigen Stellen.
  - `language-packs/rust.md` — Rust-Pack, extrahiert aus den real gelebten Konventionen im Sodium-Repo (Cargo-Setup, edition-2024-Pitfalls, cargo-deny/cargo-about, UniFFI-Hinweise, Test-Position, Error-Idiom).
  - `language-packs/_template.md` — Skelett des Language-Pack-Interfaces aus §5 für die zukünftigen Packs (Go, TS, Kotlin).
  - `BOOTSTRAP.md` — der konkrete Schrittablauf aus §6.
  - `README.md` — was das Repo ist, wie man es benutzt.

**Methode für Claude Code:**

1. Lies `BLUEPRINT-SPEC.md` vollständig.
2. Lies aus dem Sodium-Repo: `CLAUDE.md`, `PLAN.md`, `docs/HANDOFF.md`, mindestens drei `docs/adrs/*.md` (verschiedene Inhaltsklassen), mindestens drei `docs/plan/*.md`, die existierende Findings-Datei (oder Findings-Sektionen, wo immer Sodium sie hält), `reference/README.md`, `.gitignore`, `.claude/settings.json` (falls vorhanden).
3. Für jede Pflicht-Datei aus §4: extrahiere die **Struktur** und **Konventionen** aus dem Sodium-Pendant. Trenne projekt-agnostisch von Sodium-spezifisch:
   - Projekt-agnostisch (Sektions-Schema, Update-Protokolle, Format-Konventionen) → ins Core-Skeleton.
   - Sprachspezifisch-aber-Rust (Cargo-Befehle, edition-2024-Pitfall, Test-Position, Error-Idiom, UniFFI-Hinweise inkl. der Bindings-Generierung-Sektion) → ins `rust.md`.
   - Sodium-spezifisch (Wire, MLS, Kalium, Natrium, CivicSeal-Bezüge) → wird zum Placeholder oder weggelassen.
4. **Findings-Migration:** Sodium hat aktuell vermutlich noch das alte `findings.md`-Format (numerierte Sektionen in einer Datei). Im Blueprint-Skeleton wird das neue Index+Volltext-Modell verwendet (§4.6). Die Konvertierung Sodiums selbst auf das neue Modell ist ein **separater Vorgang außerhalb dieses Bootstraps** und nicht Teil von Phase 2.
5. **Numerierungs-Konvention:** Im Blueprint dreistellig (`001-`, `NNN`). Sodium nutzt aktuell zweistellig für Steps und vierstellig für ADRs — das ist Sodium-internes Erbe und wird *nicht* ins Blueprint übernommen.
6. **Multi-Language-Berücksichtigung:** Das Blueprint-Skeleton zeigt den Single-Language-Fall (per Default). §6 gibt den Mechanismus für polyglotte Komponenten vor — Claude Code muss nur sicherstellen, dass das Bootstrap-Tool die Liste mehrerer Language-Packs entgegennehmen kann (Datenstruktur-Frage, kein Skeleton-Inhalt).
7. Erzeuge die Skeleton-Files mit:
   - HTML-Kommentaren (`<!-- ... -->`) an jedem Sektions-Anfang, die kurz erklären, was reinkommt.
   - `{{PLACEHOLDER}}`-Markierungen an allen komponente-spezifischen Stellen.
   - Konkrete Beispiel-Inhalte, wo das Format ohne sie nicht klar wäre (z.B. ADR-Optionen-Block mit Pro/Con; FINDINGS.md-Tabelle mit zwei Beispielzeilen).
8. Zeig die geplante Datei-Liste mit Größenschätzung an, bevor du sie schreibst — damit Volker ggf. korrigieren kann, bevor 20+ Files gleichzeitig generiert werden.

**Validation am Ende von Phase 2:**

Sodium selbst muss sich rückwirkend als Instanz dieses Blueprints lesen lassen. Konkreter Check: Wenn man `core/CLAUDE.md` als Schablone nimmt und Sodium-spezifische Inhalte einsetzt, sollte die heutige `Sodium/CLAUDE.md` rauskommen (modulo später hinzugekommener Sektionen und der bewussten Format-Migrationen wie Findings-Index/Volltext und 3-stellige Numerierung). Wo das nicht passt, ist entweder das Blueprint zu eng oder Sodiums CLAUDE.md hat eine Eigenheit, die in den Blueprint zurückgespielt werden sollte.

---

## §10 Open Questions

1. ~~**Bootstrap-Tool oder reine Markdown-Anleitung?**~~ **Resolved (v2, 2026-05-11):** Weder noch — der Bootstrap ist Claude-Code-driven via Top-Level `CLAUDE.md` im Blueprint-Repo. Siehe §7.
2. **Master-Repo für die Language-Packs**, oder Pack-Doc beim Bootstrap reinkopieren und ab dann komponenten-lokal pflegen? Erstes ist konsistenter, zweites ist robuster gegen Pack-Bugs.
3. **README.md-Konventionen** — soll es im Blueprint ein README-Template geben oder bleibt das frei? Tendenz: leichtes Template (Sektionen: What is this / Quick start / Documentation map / License), keine harten Vorgaben.
4. **CI-Konfiguration** im Blueprint? Wenn ja, welcher CI-Anbieter ist Default bei Schwarz Digits? (GitHub Actions / GitLab CI / etwas Internes?)
5. **`.editorconfig`, `.gitattributes`, `.github/PULL_REQUEST_TEMPLATE.md`** — alles plausibel für den Blueprint, nicht in der ersten Iteration, aber als Phase-3-Erweiterung sinnvoll.

---

## §11 Revision History

- 2026-05-08: Initial draft v1.
- 2026-05-08: Draft v2 — Volkers Review eingearbeitet:
  - `findings.md` → `FINDINGS.md` (Index) + `docs/findings/` (Volltext, eine Datei pro Finding mit Status)
  - Komponenten-spezifische Pflicht-Docs in `docs/` explizit gemacht: `architecture.md`, `threat-model.md`, `api.md`
  - Strict TDD als Hard Rule und Meta-Rule verankert
  - ADRs jetzt drei-stellig (`NNN`) statt vier-stellig
  - Step-Files jetzt drei-stellig (`NNN`) statt zwei-stellig
  - Neue §6 Multi-Language Components mit drei Konstellationen (KMP-Stil, Bindings-Generation, polyglott-gekoppelt) und Split-Kriterium
  - Numbering-Immutability als Meta-Rule ergänzt
- 2026-05-09: Draft v3 — Lessons aus Phase-2-Output:
  - Placeholder-Notation in zwei Klassen aufgeteilt: Mustache `{{NAME}}` für Bootstrap-Replacements, Angle-Bracket `<NAME>` für Pattern-Notation, die im instantiierten Repo stehen bleibt. Vorher war nur Mustache definiert, was zu Kollisionen mit Branch-Naming- und Commit-Prefix-Schemata in CLAUDE.md führte.
- 2026-05-09: Draft v3 — Folgekorrekturen aus Phase-2-Audit:
  - §4.4 ADR-Schablone, §4.5 Step-Schablone, §4.6 Findings-Schablone: Mustache durch Angle-Bracket ersetzt für alle Per-Instanz-Werte (`<NNN>`, `<Title>`, `<branch-name>`, `<test-N-description>` …). Bootstrap-Werte bleiben mustache.
  - `{{COMPONENT_NAME}}` (Underscore) durchgängig auf `{{COMPONENT-NAME}}` (Hyphen) normalisiert. §7 Bootstrap-Frage-Labels analog auf Hyphen umgestellt.
  - `{{lang}}` → `{{language-pack}}` und `{{genesis-slug}}` → `{{genesis-decision-slug}}` (kanonische Namen).
  - §5 H1: `{{Language}}` → `<Language>` (per-Pack-Wert, keine Bootstrap-Replacement).
  - §4.10 Querverweis von §6 auf §7 korrigiert.
  - §7 Bootstrap-Fragen-Liste um `LICENSE` (SPDX-Identifier) ergänzt.
  - §6.3: `{{primary}}` / `{{secondary}}` umbenannt zu `{{primary-language-pack}}` / `{{secondary-language-pack}}` (kanonische, eindeutige Namen). §7 nennt die beiden Polyglot-Antworten separat als `PRIMARY-LANGUAGE-PACK` und `SECONDARY-LANGUAGE-PACK[]`.
- 2026-05-11: Draft v3 — siebte Pflicht-Hard-Rule "Tidyings stay separate from behavioural changes" in §4.1 ergänzt. Quelle: Kent Beck, *Tidy First?*. Slot zwischen "Documentation update happens in the same commit" und "Accepted ADRs are not edited" — passt thematisch (beide Nachbarn betreffen Commit-Struktur bzw. Commit-Lifecycle). Mandatory-Cap bei 10 nicht überschritten (7/10 belegt).
- 2026-05-11: Draft v4 — Bootstrap-Pivot zu Claude-Code-driven Flow:
  - §3 Directory Layout: `BOOTSTRAP.md` aus dem Component-Root-Layout entfernt. Der Bootstrap-Pfad ist jetzt blueprint-intern.
  - §4.10 (BOOTSTRAP.md transient): entfernt. Die ganze Sektion entfällt, weil es im Component-Repo kein BOOTSTRAP.md mehr gibt.
  - §7 Bootstrap Process: komplett umgeschrieben für Claude-Code-driven Flow. Der Bootstrap erfolgt durch Claude im interaktiven Dialog (Sprach-Auswahl Englisch → gewählte Sprache → 7 Bootstrap-Fragen → Plan → Ausführung → Restart-Anweisung). Strukturiert in §7.1 (User-Aktion), §7.2 (Claude-Workflow), §7.3 (Failure recovery: nuke-and-re-clone, keine idempotenten Re-Runs), §7.4 (kein manueller Fallback — wenn Claude nicht verfügbar, macht ein Claude-Code-Projekt ohnehin keinen Sinn).
  - §10 OQ-1 (Bootstrap-Tool vs. Markdown-Anleitung): als resolved markiert.
  - Im Blueprint-Repo: Top-Level `CLAUDE.md` (Bootstrap-Modus-Prompt) angelegt, `core/BOOTSTRAP.md` (manuelle Checkliste) gelöscht, Top-Level `README.md` auf Claude-driven Quickstart verkürzt.
