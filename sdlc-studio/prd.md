<!--
Template: Product Requirements Document (Streamlined)
File: sdlc-studio/prd.md
Status values: See reference-outputs.md
Related: help/prd.md, reference-prd.md
-->
# Product Requirements Document

**Project:** calculator-poc
**Version:** 0.3.0
**Last Updated:** 2026-07-16
**Status:** Draft

---

## 1. Project Overview

### Product Name

Calculator POC (with themeable UI)

### Purpose

A small, self-contained web application that performs everyday basic
arithmetic and lets the user personalise the interface via a full custom
colour picker. It is a proof of concept: the goal is to validate a clean,
componentised, type-safe frontend build and a persisted theming approach
that can later be lifted into larger Inchcape web products.

### Tech Stack

- **Language:** TypeScript
- **UI framework:** React (function components + hooks)
- **Build tool:** Vite
- **Styling:** CSS custom properties (CSS variables) driven by theme state,
  scoped component styles (CSS Modules or plain CSS — decided at TRD)
- **State/persistence:** React state for calculation; `localStorage` for the
  saved custom theme
- **Testing:** Vitest + React Testing Library (unit/component), optional
  Playwright for a smoke e2e (decided at TRD)

No backend, database, or external network calls are in scope for the POC.

### Architecture Pattern

Single-page, client-only React application. Two cohesive concerns:

1. **Calculator engine** — a pure, framework-agnostic module holding the
   arithmetic/evaluation logic and calculator state machine.
2. **Theme system** — a React context/provider that owns the active theme,
   applies it as CSS variables on a root element, and persists it.

Keeping the engine pure (no React imports) is deliberate so it is unit-testable
in isolation and portable.

---

## 2. Problem Statement

### Problem Being Solved

Teams need a lightweight, trustworthy reference implementation of a modern
React + TypeScript + Vite frontend: correct arithmetic behaviour, accessible
interaction, and user-personalisable theming that survives reloads. Building
this as a POC surfaces the patterns (pure engine, themable design tokens,
persistence) without the weight of a production app.

### Target Users

- **End user** — anyone using the calculator in a browser to do quick sums and
  set a colour theme they like.
- **Developer / evaluator (secondary)** — Inchcape engineers assessing the
  build setup, component structure, and theming approach for reuse.

### Context

- Greenfield: the repository currently contains only a README and Claude
  settings. Everything below is *to be built* (create mode); all features are
  therefore **Not Started**.
- Scope was confirmed with the requester: basic arithmetic, a **full custom
  colour picker** (not just presets), persisted across visits, on a
  React + TypeScript + Vite frontend.

### POC Success Criteria

The POC is "done and worth graduating" when all of the following hold (this is the
sign-off bar the buyer/sponsor reads — added from the team consult, C1):

1. **All must-have features (F1–F7) work** and are demonstrable in a browser.
2. **The test suite is green** and covers the arithmetic edge cases (incl.
   divide-by-zero, float formatting, operator-replacement, percentage) and the
   theme persistence round-trip (save → reload → restore).
3. **The theming legibility guarantee holds** — no accent a user can pick produces
   text/labels below WCAG AA on any surface (F9), and there is no flash of the
   default theme on load (F7).
4. **The decisions (D1–D6) are honoured** by the implementation, not quietly
   reopened.
5. **The build is lightweight and portable** — no heavy dependencies; the pure
   engine has zero React/DOM imports and could be reused elsewhere.

Explicitly **out of scope** for success: scientific functions, memory keys,
multi-token theming, a backend, accounts, or any Playwright e2e.

---

## 3. Feature Inventory

| Feature | Description | Status | Priority | Location |
|---------|-------------|--------|----------|----------|
| F1 Basic arithmetic | Add, subtract, multiply, divide with chained operations | Not Started | Must-have | TBD (`src/engine/`) |
| F2 Display & entry | Number entry, decimal point, running display, result | Not Started | Must-have | TBD (`src/components/Display`) |
| F3 Clear & sign controls | Clear/all-clear, +/- sign toggle, percentage | Not Started | Must-have | TBD (`src/engine/`) |
| F4 Error handling | Divide-by-zero and overflow handled gracefully | Not Started | Must-have | TBD (`src/engine/`) |
| F5 Keypad UI | Clickable button grid, responsive layout | Not Started | Must-have | TBD (`src/components/Keypad`) |
| F6 Custom colour theme picker | Pick UI colours via a colour picker | Not Started | Must-have | TBD (`src/theme/`) |
| F7 Theme persistence | Chosen theme saved and restored across visits | Not Started | Must-have | TBD (`src/theme/`) |
| F8 Keyboard input | Type digits/operators; Enter=equals, Esc=clear | Not Started | Should-have | TBD |
| F9 Accessibility | Keyboard-operable, ARIA labels, sufficient contrast | Not Started | Should-have | Cross-cutting |
| F10 Reset theme to default | One action to revert to the default theme | Not Started | Nice-to-have | TBD (`src/theme/`) |

### Feature Details

#### F1 Basic arithmetic

**User Story:** As an end user, I want to add, subtract, multiply and divide
numbers so that I can perform everyday calculations.

**Acceptance Criteria:**

- [ ] The four operations `+`, `−`, `×`, `÷` each produce the mathematically
      correct result for two operands.
- [ ] Operations can be chained: entering `2 + 3 × 4` using immediate-execution
      calculator semantics evaluates left-to-right as a physical calculator
      does (`(2 + 3) × 4 = 20`), and this ordering is documented as intentional.
- [ ] Pressing equals (`=`) repeatedly repeats the last operation against the
      last operand (e.g. `2 + 3 = 5`, `= 8`, `= 11`).
- [ ] Pressing an operator after a result begins a new operation using that
      result as the first operand.
- [ ] **Operator replacement:** pressing a second operator without an intervening
      operand replaces the pending operator (`2 + × 3 = 6` — the `×` supersedes
      the `+`); no calculation happens on the operator press.
- [ ] **Bare equals:** pressing `=` with no pending operation leaves the current
      operand unchanged (e.g. `5 =` → `5`), never an error.
- [ ] Results are rounded/formatted to avoid floating-point noise (e.g.
      `0.1 + 0.2` displays `0.3`, not `0.30000000000000004`).

**Dependencies:** F2 (display), engine module
**Status:** Not Started
**Confidence:** [HIGH] (scope explicitly confirmed)

#### F2 Display & entry

**User Story:** As an end user, I want to see what I'm typing and the result so
that I can trust the calculation.

**Acceptance Criteria:**

- [ ] Digits `0`–`9` append to the current entry; leading-zero is collapsed
      (`0`, then `5` → `5`, not `05`).
- [ ] A single decimal point can be entered; a second `.` in the same operand
      is ignored.
- [ ] The display shows the current operand while typing and the result after
      `=`.
- [ ] Very long results are handled without breaking layout: results are capped
      to a fixed number of significant digits, and values whose magnitude exceeds
      what the display fits switch to **scientific notation** (e.g. `1.2345e12`)
      rather than overflowing. (Exact digit cap set at TRD.)

**Dependencies:** F1
**Status:** Not Started
**Confidence:** [HIGH]

#### F3 Clear & sign controls

**User Story:** As an end user, I want to clear my input and adjust sign or
percentage so that I can correct mistakes and do common conversions.

**Clear model (decided):** a single **`AC` (all-clear)** key plus **`Backspace`**
to delete the last entered digit. There is no separate clear-entry (`C`) key.

**Percentage model (decided — D6):** `%` follows standard desk-calculator
semantics. **Standalone** (no pending operation) it divides the current operand
by 100 (`50 %` → `0.5`). **In an operation** it takes that percentage *of the
first operand*: for `A + B %` the `%` yields `A × B/100`, so `200 + 10 % =` gives
`220` (10% of 200 = 20, added). The same rule applies to `−`, `×`, `÷`.

**Acceptance Criteria:**

- [ ] `AC` (all-clear) resets the display to `0` and clears all pending state
      (stored operand, pending operator, error flag).
- [ ] `Backspace` removes the last entered digit of the current operand; when the
      operand is reduced to empty it shows `0`. `Backspace` has no effect
      immediately after `=` (a result is not editable digit-by-digit) — a new
      entry or operator starts fresh.
- [ ] `+/−` toggles the sign of the current operand.
- [ ] `%` **standalone** divides the current operand by 100 (`50 %` → `0.5`);
      `% ` of `0` yields `0`, never an error.
- [ ] `%` **in an operation** takes the percentage of the first operand per D6
      (`200 + 10 % =` → `220`; `200 − 10 % =` → `180`; `200 × 10 % =` → `20`).
- [ ] **Decimal after operator:** pressing `.` as the first character of a new
      operand yields `0.` (a leading zero is supplied), not a bare `.`.

**Dependencies:** F1, F2
**Status:** Not Started
**Confidence:** [HIGH] (clear model and percentage semantics both decided — D3, D6)

#### F4 Error handling

**User Story:** As an end user, I want the calculator to fail gracefully so that
a bad input doesn't leave it in a broken state.

**Acceptance Criteria:**

- [ ] Division by zero shows a clear, non-crashing error state (e.g. `Error`)
      rather than `Infinity` or `NaN`.
- [ ] From an error state, `AC` restores a usable `0` display.
- [ ] Results exceeding the display capacity are handled per the F2 long-result
      rule rather than corrupting the display.

**Dependencies:** F1
**Status:** Not Started
**Confidence:** [HIGH]

#### F5 Keypad UI

**User Story:** As an end user, I want a clear button layout so that I can
operate the calculator by pointer or touch.

**Acceptance Criteria:**

- [ ] All digit, operator, and control keys are rendered as buttons in a
      conventional calculator grid.
- [ ] Layout is responsive and usable on a narrow (mobile) viewport and a wide
      (desktop) viewport.
- [ ] Buttons give visible interaction feedback (hover/active/focus states).

**Dependencies:** F2
**Status:** Not Started
**Confidence:** [HIGH]

#### F6 Custom colour theme picker

**User Story:** As an end user, I want to choose the calculator's colours with a
colour picker so that I can personalise its appearance to my taste.

**Theme model (decided):** the user picks a **single accent colour** via a full
colour picker. All other tokens — page background, surface/keypad background, and
**text/label colours** — are **derived** from that accent (see F9 for the luminance
rule). This keeps the UX to one decision and makes an unreadable result structurally
hard to produce.

**Acceptance Criteria:**

- [ ] The UI exposes a colour picker for a single accent colour (native
      `<input type="color">` unless a richer control is justified at TRD).
- [ ] Selecting a colour updates the calculator's appearance immediately
      (live preview, no reload).
- [ ] Background and surface tokens are derived deterministically from the chosen
      accent (e.g. tint/shade steps), and applied via CSS custom properties on a
      root element so all components restyle consistently.
- [ ] Where the accent colours a surface (e.g. operator buttons), the label/icon
      colour on that surface is derived for contrast too — **every** theme-coloured
      surface gets a legible foreground, not just the page background (see F9).
- [ ] Text colour is auto-computed for legibility against its background
      (see F9), never chosen directly by the user.

**Dependencies:** Theme provider (F7)
**Status:** Not Started
**Confidence:** [HIGH] (single-accent + derived model decided)

#### F7 Theme persistence

**User Story:** As an end user, I want my chosen theme remembered so that I
don't have to reselect it every visit.

**Acceptance Criteria:**

- [ ] The active theme is written to `localStorage` whenever it changes.
- [ ] On load, a previously saved theme is read **synchronously before first
      paint** and applied, so there is **no flash of the default theme** — this is
      a hard requirement, not best-effort. (Mechanism — e.g. a blocking inline
      script that sets the CSS variables before React mounts — is a TRD detail.)
- [ ] If no saved theme exists, the default theme is applied.
- [ ] Corrupt or unparseable stored theme data falls back to the default
      without crashing.

**Dependencies:** F6
**Status:** Not Started
**Confidence:** [HIGH] (no-flash hardened — C4)

#### F8 Keyboard input

**User Story:** As an end user, I want to use my keyboard so that I can
calculate faster.

**Acceptance Criteria:**

- [ ] Digit and operator keys (`0`–`9`, `+`, `-`, `*`, `/`, `.`) map to the
      corresponding calculator actions.
- [ ] `Enter` or `=` triggers equals; `Escape` triggers all-clear (`AC`);
      `Backspace` deletes the last entered digit (matching the F3 clear model).

**Dependencies:** F1, F2
**Status:** Not Started
**Confidence:** [HIGH] (key map aligned with the decided clear model)

#### F9 Accessibility

**User Story:** As an end user relying on assistive tech or keyboard, I want the
calculator to be operable and readable so that I can use it too.

**Legibility rule (decided):** foreground colour is **auto-computed from the
luminance of whatever it sits on** — the theme system chooses a light or dark
foreground token per surface so text/labels always meet WCAG AA contrast. This
applies to **every theme-coloured surface**: the page/display background *and*
any accent-coloured surface such as operator buttons (C3). The user cannot select
an unreadable pair because foreground colour is derived, not picked.

**Acceptance Criteria:**

- [ ] All interactive controls are reachable and operable by keyboard with a
      visible focus indicator.
- [ ] Buttons and the colour picker expose accessible names (ARIA labels where
      text alone is insufficient).
- [ ] For any accent the user picks, the derived foreground meets WCAG AA contrast
      against **each** surface it sits on — ≥ 4.5:1 for normal text, ≥ 3:1 for
      large text (the display digits qualify as large text) — verified by the
      luminance rule above, including on accent-coloured buttons.

**Dependencies:** F5, F6, F8
**Status:** Not Started
**Confidence:** [HIGH] (legibility approach decided, extended to accent surfaces — C3)

#### F10 Reset theme to default

**User Story:** As an end user, I want to reset colours so that I can return to
the original look after experimenting.

**Acceptance Criteria:**

- [ ] A visible control restores the default theme.
- [ ] Reset clears the persisted custom theme from `localStorage` (or overwrites
      it with the default).

**Dependencies:** F6, F7
**Status:** Not Started
**Confidence:** [HIGH]

---

## 4. Functional Requirements

### Core Behaviours

- The calculator uses **immediate-execution** semantics (like a physical
  desk calculator), not full expression-with-precedence parsing. This is a
  deliberate scope choice for the POC and must be stated in the relevant story
  ACs so it isn't mistaken for a bug.
- The arithmetic engine is a **pure module** with no React dependency: given a
  state and an input event, it returns the next state. This makes it unit
  testable and portable.
- Theme state is owned by a single provider and expressed as CSS custom
  properties, so styling is data-driven rather than hard-coded per component.

### Input/Output Specifications

- **Inputs:** pointer/touch on keypad buttons; keyboard keys (F8); colour
  selections from the picker (F6).
- **Outputs:** the display string; the applied set of CSS variables; the
  persisted theme record in `localStorage`.
- **Persisted record (indicative shape, finalised at TRD):**
  `localStorage["calculator-poc.theme"] = { "<tokenName>": "<cssColor>", ... }`.

### Business Logic Rules

- Divide-by-zero → error state, never `Infinity`/`NaN` on screen.
- Result formatting removes floating-point artefacts (round to a sensible
  precision, then strip trailing zeros).
- Results exceeding display capacity switch to scientific notation (F2/F4).
- Only one decimal point per operand; a leading `.` becomes `0.`.
- Sign operates on the current operand.
- Percentage (D6): standalone `÷100`; in an operation, percentage of the first
  operand (`200 + 10 % = 220`).
- A second operator with no intervening operand replaces the pending operator.
- Bare `=` (no pending operation) leaves the operand unchanged.
- Clear model: single `AC` (all-clear) + `Backspace` (delete last digit); no
  separate clear-entry key.
- Theme is a single user-chosen accent; background/surface are derived from it and
  foreground (text/labels) is auto-computed from the luminance of each surface it
  sits on for WCAG AA contrast — including accent-coloured buttons.
- Saved theme is applied synchronously before first paint (no flash of default).
- On invalid/corrupt persisted theme, silently fall back to default.

---

## 5. Non-Functional Requirements

### Performance

- Client-only; interactions (keypress → display update, colour change → repaint)
  should feel instant (< ~100 ms perceived) on a modern browser.
- Production build should be small (a calculator POC) — no heavy dependencies;
  favour the platform (native `<input type="color">` or a lightweight picker).

### Security

- No backend, no auth, no PII. Only a non-sensitive theme preference is stored
  in `localStorage`.
- Stored theme values must be validated/sanitised on read before being injected
  as CSS values (avoid trusting arbitrary stored strings blindly).

### Scalability

- Not applicable in the traditional sense (single static frontend). Must serve
  as static assets from any static host / CDN.

### Availability

- No uptime target for a POC beyond "works when served." No server-side
  dependency to be unavailable.

---

## 6. AI/ML Specifications

> Not applicable. The POC contains no AI/ML components.

---

## 7. Data Architecture

### Data Models

- **CalculatorState** (in-memory only): current operand, stored operand,
  pending operator, overwrite/entry flags, error flag. Exact shape defined at
  TRD/story level.
- **Theme**: a map of design-token names to CSS colour values.

### Relationships and Constraints

- `Theme` is the single source of truth for colours; components read CSS
  variables derived from it, never hard-coded colours.
- `CalculatorState` is transient and never persisted.

### Storage Mechanisms

- **In-memory (React state):** calculator state.
- **`localStorage`:** the active `Theme` only, under a namespaced key. No other
  persistence.

---

## 8. Integration Map

### External Services

- None. No network calls in scope.

### Authentication Methods

- None.

### Third-Party Dependencies

- Runtime: React, ReactDOM. Optionally a small colour-picker library **only if**
  `<input type="color">` proves insufficient for the required token set
  (decision recorded at TRD).
- Tooling: Vite, TypeScript, Vitest, React Testing Library, ESLint/Prettier
  (dev only).

---

## 9. Configuration Reference

### Environment Variables

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| (none) | The POC requires no environment variables | No | — |

### Feature Flags

- None planned for the POC.

---

## 10. Quality Assessment

### Tested Functionality

- Nothing yet — greenfield. **Test scope (decided): Vitest unit + React Testing
  Library component tests; no Playwright e2e for the POC.** Target coverage:
  - Unit tests for the pure calculator engine covering each operation, chaining,
    repeat-equals, operator-replacement, bare-`=`, decimal rules
    (incl. decimal-after-operator), sign, percentage (standalone and
    in-operation per D6, incl. `%`-of-zero), backspace, and divide-by-zero.
  - Component tests for keypad interaction, display updates, accent-picker →
    derived-theme application, auto-computed foreground contrast on every
    surface (incl. accent-coloured buttons), no-flash-on-load, and the
    persistence round-trip (save → reload → restore).

### Untested Areas

- All areas until implementation begins.

### Technical Debt

- None yet. Known deliberate scope limits (not debt, but to record): immediate-
  execution semantics rather than operator precedence; basic arithmetic only
  (no scientific functions or memory keys).

---

## 11. Open Questions

_All initial open questions have been resolved (see Resolved Decisions below).
None outstanding._

---

## 11a. Resolved Decisions

| # | Question | Decision | Affects |
|---|----------|----------|---------|
| D1 | Which theme tokens does the picker expose? | **Single accent colour**, chosen by the user; background/surface derived from it. | F6, F7 |
| D2 | How is legibility guaranteed for arbitrary picks? | **Auto-compute text colour** from background luminance to meet WCAG AA. | F6, F9 |
| D3 | Clear/delete model? | **Single `AC` (all-clear) + `Backspace`** (delete last digit); no separate `C`. | F3, F8 |
| D4 | Automated test scope? | **Vitest unit + React Testing Library component tests**; no Playwright e2e for the POC. | §10 |
| D5 | Long-result display strategy? | Cap to a fixed number of significant digits; **switch to scientific notation** on overflow. | F2, F4 |
| D6 | Percentage-in-operation semantics? | **Standard desk-calculator rule**: standalone `%` is `÷100`; in an operation it takes the percentage of the first operand (`200 + 10 % = 220`). | F3 |

> D5 was defaulted (least contentious) and can be revisited at TRD; the exact
> significant-digit cap is a TRD detail. D6 resolves the last soft AC, raised in
> the 2026-07-16 team consult.
>
> **Consult refinements (2026-07-16), not new decisions but tightened requirements:**
> C3 — the legibility guarantee (D2) extends to *every* theme-coloured surface,
> including accent-coloured buttons (F6, F9). C4 — the saved theme applies
> synchronously before first paint; no-flash is a hard requirement (F7).
> C5 — operator-replacement, bare-`=`, `%`-of-zero and decimal-after-operator
> edge cases are now enumerated in F1/F3 ACs. C1 — a POC Success Criteria section
> was added (§2).

---

## Changelog

| Date | Version | Changes |
|------|---------|---------|
| 2026-07-16 | 0.1.0 | Initial PRD authored in create mode (greenfield). Scope confirmed with requester: basic arithmetic, full custom colour picker with `localStorage` persistence, React + TypeScript + Vite frontend. |
| 2026-07-16 | 0.2.0 | Resolved all 5 open questions (D1–D5): single-accent derived theming, auto-computed text contrast, single AC + Backspace clear model, Vitest unit+component test scope, scientific-notation overflow. Updated F2, F3, F6, F8, F9, §4, §10; added Resolved Decisions table. |
| 2026-07-16 | 0.3.0 | Folded team consult findings: D6 (percentage semantics); C1 POC Success Criteria (§2); C3 contrast extended to accent-coloured surfaces (F6, F9); C4 hard no-flash on load (F7); C5 edge cases — operator-replacement, bare-`=`, `%`-of-zero, decimal-after-operator (F1, F3). Updated §4. |

---

> **Confidence Markers:** [HIGH] clear from code | [MEDIUM] inferred from patterns | [LOW] speculative
>
> **Status Values:** Complete | Partial | Stubbed | Broken | Not Started
