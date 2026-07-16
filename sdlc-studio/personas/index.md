<!--
Template: Persona Index
File: sdlc-studio/personas/index.md
Purpose: Lists all personas active for this project
Related: reference-persona.md, help/persona.md
-->
# Project Personas

Working team seats for **calculator-poc**, grown from PRD v0.2.0. These are review seats
(the resolver keys on each card's declared `<!-- role: -->`, not its filename or heading).

**Last updated:** 2026-07-16

---

## Team Seats

Build-and-review seats (dual render). Each critiques as a separate instance from the one that
built — a seat never signs off its own work.

### Product Amigo

| Persona | Role | Summary | File |
|---------|------|---------|------|
| Mara Feldt | product | Guards the POC's scope boundary; every feature must trace to a stated need; keeps D1–D5 decided | [Details](seats/mara-feldt.md) |

### Engineering Amigo

| Persona | Role | Summary | File |
|---------|------|---------|------|
| Tomas Halloran | engineering | Keeps the arithmetic engine pure and React-free; TDD; distrusts floating-point; guarded `localStorage` | [Details](seats/tomas-halloran.md) |

### QA Amigo

| Persona | Role | Summary | File |
|---------|------|---------|------|
| Bianca Osei | qa | Boundary-first; divide-by-zero, float formatting, decimal rules, repeat-equals, persistence round-trip; every test must be able to fail | [Details](seats/bianca-osei.md) |

### UX / Accessibility Seat (extra seat)

Review-focused document-owner seat for the interaction and legibility contract. Added because
theming (F6) and accessibility (F9) are central to this POC.

| Persona | Role | Summary | File |
|---------|------|---------|------|
| Elliot Vance | ux | Owns the colour-picker UX and the WCAG AA legibility guarantee (derived text colour, D2); keyboard/ARIA operability | [Details](seats/elliot-vance.md) |

---

## Stakeholder Personas

The other side of the table — people the project answers to but who don't build. Grown from
PRD v0.2.0 and **validated by `/sdlc-studio persona review` (2026-07-16)** — both accepted, no
longer provisional.

| Persona | Type | Cast | Summary | File |
|---------|------|------|---------|------|
| Harriet Voss | buyer | Customer | POC sponsor; wants a reusable, bounded, tested themable-frontend pattern she can point other teams at | [Details](stakeholders/harriet-voss.md) |
| Owen Castellano | served | Served | End-user advocate; protects the everyday user — correct sums, easy correction, readable/reversible theming, graceful errors | [Details](stakeholders/owen-castellano.md) |

> **Arbitration rule (on every card):** a stakeholder's goals never override the Primary user's
> interface. Harriet's "reusable pattern" and Owen's advocacy are met without overruling what the
> end user needs at the keypad.

---

## Consultation Defaults

Default seats consulted for each artefact type.

| Artefact | Team | Stakeholders |
| -------- | ---- | ------------ |
| PRD | Product | Harriet (buyer), Owen (end-user) |
| Epic | Product, Engineering | Harriet (scope/adoption) |
| User Story | Product, + UX for theming/accessibility stories | Owen (end-user impact) |
| Technical Spec (TRD) | Engineering, UX (legibility/contrast) | Harriet (dependency footprint) |
| Test Strategy (TSD) | QA | Owen (does it cover real user slips?) |

Override defaults with `--persona` / `--role`, or `--skip-personas` to bypass.

---

## Usage

```bash
# Three Amigos review of an artefact
/sdlc-studio consult team sdlc-studio/prd.md

# Consult a single seat by role
/sdlc-studio consult --role ux sdlc-studio/epics/EP<id>.md

# Resolve a seat's review framing (used by delegated reviewers)
python3 "$CLAUDE_SKILL_DIR/scripts/persona_resolve.py" resolve-consult --role engineering --root .
```

---

## Persona Sources

| Source | Count | Notes |
| ------ | ----- | ----- |
| Generated (team seats) | 4 | Grown from PRD v0.2.0 (`persona generate --team`) — accepted |
| Generated (stakeholders) | 2 | Grown from PRD v0.2.0 (`persona generate --stakeholders`) — validated & accepted (`persona review`, 2026-07-16) |
| Archetypes | 0 | — |
| Imported | 0 | — |

Team cast: 4 (Product, Engineering, QA, UX) — within the 3–5 guideline.
Stakeholder panel: 2 (buyer, served) — deliberately lean; no compliance/ops signal for a low-stakes POC.

---

## Review History

| Date | Action | Outcome |
|------|--------|---------|
| 2026-07-16 | `persona review` | Both stakeholders (Harriet, Owen) validated well-formed with testable veto lines; provisional stamps cleared → accepted. |

**Known gap (deliberate):** no **Primary design persona** — the individual end user at the keypad —
is modelled. Owen (served) advocates *for* that user but is not a substitute. Decided **not** to
generate one for this POC; revisit if `story` work needs concrete `As a <persona>` lines.

---

*See [reference-persona.md](../../reference-persona.md) for detailed persona workflows.*
