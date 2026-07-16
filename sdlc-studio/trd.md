<!--
Template: Technical Requirements Document (Streamlined)
File: sdlc-studio/trd.md
Status values: See reference-outputs.md
Modules: trd/c4-diagrams.md, trd/container-design.md, trd/adr.md
Related: help/trd.md, reference-trd.md
-->
# Technical Requirements Document

**Project:** calculator-poc
**Version:** 0.1.0
**Status:** Draft
**Last Updated:** 2026-07-16
**PRD Reference:** [PRD](./prd.md)

---

## 1. Executive Summary

### Purpose

This TRD defines the technical design for the Calculator POC: a small,
self-contained, client-only React + TypeScript web application that performs
everyday basic arithmetic and lets the user personalise the UI via a single
custom accent colour that persists across visits. It translates the product
requirements (PRD F1–F10, decisions D1–D6) into an implementable architecture,
technology stack, and data/security model.

### Scope

**In scope:** the single-page frontend architecture; the pure calculator engine
module; the theme system (single-accent picker, derived tokens, auto-computed
legible foregrounds, `localStorage` persistence, no-flash-on-load); the test
strategy (Vitest + React Testing Library); the build/tooling setup (Vite,
TypeScript, ESLint/Prettier); and the static-hosting deployment model.

**Out of scope:** any backend, database, network calls, authentication, or
accounts; scientific/memory calculator functions; multi-token theming; and
Playwright e2e (D4). API Contracts (§5) and Integration Patterns (§7) are
therefore **Not Applicable** and retained only for structural completeness.

### Key Decisions

- **Client-only single-page application** — no backend, DB, or network I/O; the
  whole product ships as static assets to any CDN/static host.
- **Pure, framework-agnostic calculator engine** — a reducer-style module
  (`(state, event) -> state`) with **zero React/DOM imports**, so it is
  unit-testable in isolation and portable.
- **Single-accent, derived, self-legibilising theme system** — the user picks
  one accent; background/surface tokens are derived from it and every
  foreground (text/label) token is auto-computed from the luminance of the
  surface it sits on to guarantee WCAG AA contrast (D1, D2, C3).
- **`localStorage` persistence applied synchronously before first paint** — a
  blocking pre-React step sets the CSS variables so there is no flash of the
  default theme (F7, C4).
- **Vitest + React Testing Library only** — unit tests for the engine,
  component tests for UI/theme; no e2e for the POC (D4).

---

## 2. Project Classification

**Project Type:** web_application

**Classification Rationale:** The product serves a browser-based user interface
(a React SPA). It has no server-side component of its own — it is a *client-only*
web application whose "backend" is the browser platform (`localStorage`, the
DOM, native form controls). It is therefore classified as a Web Application with
a single-page, client-only sub-type rather than an API/Mobile backend or a
desktop/CLI tool.

**Architecture Implications:**

- **Default Pattern:** Monolith (single deployable frontend).
- **Pattern Used:** Client-only single-page application (a single static bundle;
  a "monolith" with no server tier).
- **Deviation Rationale:** The conventional Web Application default assumes a
  frontend *and* backend. This POC deliberately has no backend: all state is
  in-memory (calculation) or in `localStorage` (theme). The deviation is
  intentional and central to the POC's "lightweight and portable" success
  criterion.

---

## 3. Architecture Overview

### System Context

A user opens the app in a browser. All computation and theming happen locally in
the browser; nothing leaves the device. The only persistent boundary is the
browser's `localStorage`, which holds the single saved theme record. There are
no external systems, APIs, or services.

### Architecture Pattern

Client-only single-page application (monolith, no server tier).

**Rationale:** The problem is entirely a client-side interaction: arithmetic on
in-memory state plus locally-persisted UI preferences. Introducing any server
tier would add operational weight with no functional benefit and would violate
the POC's portability/lightweight goals. Two cohesive internal concerns — a
**pure calculator engine** and a **theme system** — are kept decoupled so the
engine stays testable and reusable.

### Component Overview

| Component | Responsibility | Technology |
|-----------|---------------|------------|
| Calculator engine | Pure arithmetic + calculator state machine: `(state, event) -> state`. Owns operations, chaining, repeat-`=`, operator-replacement, bare-`=`, decimal rules, sign, percentage (D6), backspace, divide-by-zero, and result formatting (incl. scientific-notation overflow). **No React/DOM imports.** | TypeScript (pure module) |
| Calculator UI (App, Display, Keypad) | Renders the display and keypad grid; translates pointer/touch and keyboard input into engine events; renders engine output. | React (function components + hooks), TypeScript |
| Theme system (provider + derivation + persistence) | Owns the active theme (single accent), derives background/surface tokens, auto-computes legible foreground tokens per surface, applies tokens as CSS custom properties on a root element, and persists/restores via `localStorage`. | React Context + TypeScript; CSS custom properties |
| Pre-paint theme bootstrap | Reads the saved theme and sets the CSS variables **before React mounts** so there is no flash of the default theme. | Inline/blocking script or equivalent early-run module |
| Colour derivation + contrast utilities | Pure helpers: derive tints/shades from an accent; compute relative luminance and choose a WCAG-AA-passing foreground per surface. | TypeScript (pure module) |

> **C4 Diagrams:** Use `trd create --with-diagrams` or see `modules/trd/c4-diagrams.md`

---

## 4. Technology Stack

### Core Technologies

| Category | Technology | Version | Rationale |
| ---------- | ----------- | --------- | ----------- |
| Language | TypeScript | 5.x | Type safety is a stated POC goal ("clean, componentised, type-safe frontend"); catches arithmetic/state-machine and theme-token errors at compile time. |
| UI framework | React (function components + hooks) | 18.x | Componentised UI with a simple context-based theme provider; matches the reuse target ("lift into larger Inchcape web products"). |
| Build tool | Vite | 5.x | Fast dev server + lightweight, tree-shaken production build; first-class TS/React support; keeps the bundle small per the "lightweight and portable" criterion. |
| Styling | CSS custom properties (CSS variables) | — | Data-driven theming: a single set of variables on a root element restyles every component, so colours are never hard-coded per component. Component-scoping mechanism (CSS Modules vs plain CSS) is an open question (Q1). |
| Calculation state | React state (in-memory) | — | `CalculatorState` is transient and never persisted; React state holds the current engine state and re-renders the display. |
| Persistence | Browser `localStorage` | — | The only persisted data is the non-sensitive theme record; `localStorage` needs no backend and survives reloads (F7). |

### Build & Development

| Tool | Purpose |
|------|---------|
| Vite | Dev server, HMR, and production bundling. |
| TypeScript compiler (`tsc`) | Type-checking (CI gate) alongside Vite's transpile. |
| Vitest | Unit + component test runner (Vite-native, fast) (D4). |
| React Testing Library | Component/interaction tests driven through the DOM as a user would (D4). |
| ESLint + Prettier | Lint and format (dev only). |

### Infrastructure Services

| Service | Provider | Purpose |
|---------|----------|---------|
| Static asset host / CDN | Any (e.g. GitHub Pages, Netlify, S3+CloudFront) | Serves the built static bundle. No server-side runtime required. |

---

## 5. API Contracts

**Not Applicable.** The POC has no backend and makes no network calls. There are
no HTTP/GraphQL/gRPC endpoints, no authentication, and no error-response
envelope. The only "contract" is the internal engine event → state interface,
documented in §6 (Data Architecture) and refined at story level.

---

## 6. Data Architecture

### Data Models

#### CalculatorState (in-memory only, never persisted)

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| currentOperand | string | numeric string; display form | The operand currently being entered / shown. |
| storedOperand | string \| null | numeric string | The first operand held while a pending operation waits for the second. |
| pendingOperator | '+' \| '−' \| '×' \| '÷' \| null | one of four ops | The operator awaiting its second operand (replaced on a second operator press). |
| overwrite | boolean | — | When true, the next digit replaces the display (e.g. immediately after `=` or an operator). |
| lastOperator / lastOperand | operator / string \| null | — | Support repeat-`=` (repeat last operation against last operand). |
| error | boolean | — | Set on divide-by-zero / invalid state; cleared by `AC`. |

> Exact field set/shape is finalised at story level; the engine is a pure
> reducer `(state, event) -> state`.

#### Theme (persisted)

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| accent | string (CSS colour) | validated/sanitised on read | The single user-chosen accent colour; the source of truth for the theme. |
| (derived tokens) | map<string, CSS colour> | derived, not stored-of-record | Background, surface, and per-surface foreground tokens computed from `accent`. |

> Persisted record (indicative, finalised at story level):
> `localStorage["calculator-poc.theme"] = { "accent": "#rrggbb" }` (derived
> tokens may be recomputed on load rather than stored, to keep the record
> minimal and forward-compatible).

### Storage Strategy

| Data Type | Storage | Rationale |
|-----------|---------|-----------|
| CalculatorState | In-memory (React state) | Transient; must reset on reload; never persisted. |
| Theme (accent) | `localStorage` (namespaced key `calculator-poc.theme`) | Must survive reloads (F7); non-sensitive; no backend available or wanted. |

### Migrations

No schema migrations in the traditional sense. Forward-compatibility for the
persisted theme is handled defensively: on read, corrupt/unparseable/unknown
data falls back to the default theme without crashing (F7). Keeping the stored
record minimal (accent only) reduces future migration surface.

---

## 7. Integration Patterns

**Not Applicable.** No external services, third-party APIs, message queues, or
event buses. The only third-party runtime dependencies are React/ReactDOM (and,
*only if* the native colour control proves insufficient, a small colour-picker
library — see Q2). All other dependencies are dev-only tooling.

---

## 8. Infrastructure

### Deployment Topology

A single static bundle (HTML + JS + CSS) produced by `vite build`, served as
static files. No server-side runtime, container, or orchestration is required.

### Environment Strategy

| Environment | Purpose | Characteristics |
| ------------- | --------- | ----------------- |
| Development | Local development | `vite dev` with HMR; no env vars required. |
| Staging | Pre-production preview (optional) | A preview deploy of the static bundle (e.g. PR preview); identical build to production. |
| Production | Live static site | `vite build` output served from a static host/CDN. |

### Scaling Strategy

Not applicable in the traditional sense — a static frontend scales via the
host/CDN edge cache. There is no server tier to scale.

> **Container Design:** Not required for the POC (static assets). Use
> `trd create --with-containers` or see `modules/trd/container-design.md` if a
> containerised static-serving image is later wanted.

---

## 9. Security Considerations

### Threat Model

| Threat | Likelihood | Impact | Mitigation |
|--------|-----------|--------|------------|
| Injection of an arbitrary/malicious CSS value via a tampered `localStorage` theme record | L | M | Validate/sanitise the stored accent on read (must be a well-formed CSS colour) before injecting it as a CSS variable; reject → fall back to default. |
| Corrupt/unparseable stored theme crashing the app on load | L | M | Defensive parse with try/catch; fall back to default theme (F7). |
| XSS via user input | L | L | No user text is rendered as HTML; input is numeric/operator events and a colour value; no `dangerouslySetInnerHTML`. |

### Security Controls

| Control | Implementation |
| --------- | ---------------- |
| Authentication | None — no accounts, no backend (out of scope). |
| Authorisation | None — no protected resources. |
| Encryption at rest | Not applicable — only a non-sensitive theme preference is stored in `localStorage`; no PII. |
| Encryption in transit | Provided by the static host over HTTPS; the app itself makes no network calls. |
| Input validation | Stored theme accent validated/sanitised on read before use as a CSS value. |

---

## 10. Performance Requirements

### Targets

| Metric | Target | Measurement |
| -------- | -------- | ------------- |
| Interaction latency (keypress → display update; colour change → repaint) | Feels instant, < ~100 ms perceived | Manual/observed on a modern browser; interactions are synchronous, pure-function driven. |
| No flash of default theme on load | Zero flash (hard requirement, F7/C4) | Saved theme applied synchronously before first paint; verified by component test. |
| Production bundle size | Small — no heavy dependencies | Inspect `vite build` output; favour platform (native `<input type="color">`) over libraries. |
| Availability | "Works when served" (no uptime target for a POC) | N/A — static assets, no server dependency. |

---

## 11. Architecture Decision Records

> See `modules/trd/adr.md` for ADR template

### ADR-001: Client-only SPA with no backend

**Status:** Accepted

**Context:** The POC does basic arithmetic and stores a UI theme preference.
There is no data to share across users or devices and no server-side logic.

**Decision:** Ship a client-only React SPA; hold calculation state in memory and
the theme in `localStorage`. No backend, database, or network calls.

**Consequences:**

- Positive: Minimal footprint; deployable as static assets to any CDN; matches
  the "lightweight and portable" success criterion; nothing to operate/secure
  server-side.
- Negative: No cross-device sync of the theme; persistence limited to the
  browser/origin (acceptable for a POC).

### ADR-002: Pure, framework-agnostic calculator engine

**Status:** Accepted

**Context:** Arithmetic correctness (chaining, repeat-`=`, operator-replacement,
percentage per D6, divide-by-zero, formatting) is the core risk and must be
thoroughly unit-tested; the engine is also a reuse target.

**Decision:** Implement the engine as a pure reducer module
`(state, event) -> state` with **zero React/DOM imports**; the UI dispatches
events and renders the returned state.

**Consequences:**

- Positive: Fully unit-testable in isolation; portable to any host; clear
  separation from rendering.
- Negative: A small amount of glue in the UI to bridge events/state (acceptable).

### ADR-003: Immediate-execution calculator semantics

**Status:** Accepted

**Context:** A physical desk calculator evaluates left-to-right as you press
keys; full operator-precedence parsing is a different, larger scope.

**Decision:** Use immediate-execution semantics (`2 + 3 × 4 = 20`), not
expression-with-precedence. State this intent in the relevant story ACs so it is
not mistaken for a bug.

**Consequences:**

- Positive: Matches user expectation for a calculator; simpler, well-bounded
  engine; deterministic.
- Negative: Not a scientific/expression calculator (explicitly out of scope).

### ADR-004: Single-accent, derived, self-legibilising theme system

**Status:** Accepted

**Context:** Arbitrary user colour choices risk unreadable text. The UX goal is
one decision with a structurally-guaranteed legible result (D1, D2, C3).

**Decision:** The user picks a single accent. Background/surface tokens are
derived deterministically from it; every foreground (text/label) token is
auto-computed from the relative luminance of the surface it sits on to meet WCAG
AA (≥ 4.5:1 normal text, ≥ 3:1 large text) — including on accent-coloured
surfaces such as operator buttons. Tokens are applied as CSS custom properties
on a root element. Foreground is never user-selectable.

**Consequences:**

- Positive: Unreadable combinations are structurally hard to produce; single-
  decision UX; consistent restyling via CSS variables.
- Negative: Users cannot independently pick text colours (intentional trade-off).

### ADR-005: Persist theme in `localStorage`, applied before first paint

**Status:** Accepted

**Context:** The chosen theme must be remembered across visits (F7) and must not
flash the default theme on load (C4, hard requirement).

**Decision:** Write the accent to `localStorage` on change; on load, read and
apply the CSS variables **synchronously before React mounts** (e.g. a blocking
pre-paint step). Validate/sanitise on read; fall back to default on missing or
corrupt data.

**Consequences:**

- Positive: No flash of default; resilient to corrupt data; no backend needed.
- Negative: Requires a small pre-React bootstrap outside the normal component
  lifecycle (mechanism is Q3).

### ADR-006: Vitest + React Testing Library only (no e2e for the POC)

**Status:** Accepted

**Context:** The POC needs trustworthy coverage of arithmetic edge cases and the
theme persistence round-trip, but Playwright e2e is explicitly out of scope
(D4).

**Decision:** Use Vitest for pure-engine unit tests and React Testing Library
for component/interaction tests. No Playwright e2e.

**Consequences:**

- Positive: Fast, lightweight test suite aligned with the engine/UI split; runs
  in CI without browsers.
- Negative: No full-browser end-to-end coverage (acceptable for a POC; can be
  added when graduating).

---

## 12. Open Technical Questions

- [ ] **Q1 (Styling scoping):** Component style scoping — CSS Modules vs plain
  CSS (global stylesheet) vs CSS-in-JS?
  **Context:** The PRD deferred this to the TRD; the requester deferred it to
  engineering. All options keep the CSS-custom-properties theming model; this
  only affects how per-component styles are scoped/authored. **Owner:**
  engineering.

- [ ] **Q2 (Colour picker control):** Native `<input type="color">` vs a
  lightweight picker library (e.g. react-colorful)?
  **Context:** The PRD prefers the native control unless a richer one is
  justified; the requester deferred to engineering. Native keeps the bundle
  smallest (§10). A library is only warranted if the required accent-selection
  UX exceeds the native control. **Owner:** engineering.

- [ ] **Q3 (No-flash mechanism):** Exact mechanism for applying the saved theme
  before first paint (blocking inline `<script>` in `index.html` that sets CSS
  variables, vs a synchronous module run before `ReactDOM.render`).
  **Context:** F7/C4 make no-flash a hard requirement (ADR-005); the concrete
  technique is an implementation choice to be validated by a component test.
  **Owner:** engineering.

- [ ] **Q4 (Display significant-digit cap):** The exact significant-digit cap
  before switching to scientific notation (D5 was defaulted).
  **Context:** F2/F4 require capping to a fixed number of significant digits and
  switching to scientific notation on overflow; the specific cap (e.g. 10–15
  significant digits) is a TRD/story detail. **Owner:** engineering.

---

## 13. Implementation Constraints

### Must Have

- The calculator engine module has **zero React/DOM imports** and is a pure
  `(state, event) -> state` reducer (ADR-002).
- Colours are data-driven via CSS custom properties on a root element; **no
  component hard-codes theme colours** (ADR-004).
- Every theme-coloured surface receives an auto-computed, WCAG-AA-passing
  foreground (D2, C3, F9).
- The saved theme is applied **synchronously before first paint** — no flash of
  the default theme (F7, C4, ADR-005).
- Stored theme data is validated/sanitised on read; corrupt data falls back to
  default without crashing (§9, F7).
- Test suite is Vitest unit (engine) + React Testing Library component tests
  covering the edge cases enumerated in the PRD (D4, §10).
- Build stays lightweight — no heavy runtime dependencies; favour the platform.

### Won't Have (This Version)

- No backend, database, network calls, authentication, or accounts.
- No scientific functions or memory keys.
- No multi-token / multi-colour theming (single accent only).
- No Playwright / full-browser e2e tests (D4).
- No operator-precedence expression parsing (immediate-execution only, ADR-003).

---

## Changelog

| Date | Version | Changes |
|------|---------|---------|
| 2026-07-16 | 0.1.0 | Initial TRD authored in create mode from PRD v0.3.0. Classified as client-only web-application SPA; captured the pure-engine + theme-system architecture, TypeScript/React/Vite/CSS-variables/localStorage stack, in-memory + localStorage data model, static-host deployment, security (sanitise stored theme; no PII), and performance targets. Recorded ADR-001..006 (client-only SPA, pure engine, immediate-execution semantics, single-accent derived legibilising theme, localStorage-before-first-paint persistence, Vitest+RTL only). Marked API Contracts and Integration Patterns Not Applicable. Deferred Q1 (styling scoping), Q2 (colour picker control), Q3 (no-flash mechanism), Q4 (significant-digit cap) to engineering as Open Technical Questions. |
