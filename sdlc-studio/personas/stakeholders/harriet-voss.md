<!-- provenance: reviewed 2026-07-16 -->
<!--
Generated stakeholder: POC sponsor / economic buyer for calculator-poc. Fresh project-native individual.
Source: Generated from PRD v0.2.0. Assumption persona until validated by `persona review`.
See stakeholder-template.md.
-->
<!-- stakeholder: buyer -->
# Harriet Voss - Head of Digital Platforms (POC sponsor)

> **Cast:** Customer (Cooper designation - Harriet mandates and funds the POC but never uses the calculator).
>
> **Arbitration:** this stakeholder's goals never override the Primary persona's interface. When
> Harriet's wants (a reusable pattern, a fast demo) conflict with what the end user needs at the
> keypad, the Primary user wins the interface and Harriet's needs are met elsewhere (the pattern
> writeup, the build/test setup, the adoption note) - the buyer-never-overrides-the-Primary rule,
> loaded with this card on every consult.

## Who They Are

Harriet owns the shared frontend direction across several Inchcape digital products and is tired of
each team reinventing theming, build tooling, and test setup slightly differently. She funded this
calculator POC not because the org needs a calculator, but because it is the smallest honest test of
one question: is React + TypeScript + Vite with token-driven theming and persisted preferences a
pattern worth standardising on? She has been burned before by "POCs" that quietly became
unmaintained production dependencies.

## What They Want

*Their End Goals for THIS project - what must be true for them to call it a success.*

1. A clean, legible reference build she can point other teams at as "this is how we do a themable frontend".
2. Evidence the pattern is sound: a pure, tested core; token-driven theming; persistence that survives reload.
3. A clear scope boundary - the POC proves the pattern without ballooning into a product that needs owning.

## Veto Lines

*What makes them block sign-off outright. Each line is testable against an artefact.*

- No POC sign-off without a passing test suite that covers the arithmetic edge cases and the theme persistence round-trip.
- No adoption recommendation if the theming approach can produce an unreadable (sub-AA-contrast) UI - the legibility guarantee (D2) must hold.
- No silent scope expansion (scientific functions, backend, accounts) that turns the POC into an unowned product.

## Evidence They Read

*What they actually look at before signing off - a consult cites these, never vibes.*

- The test report (coverage of engine edge cases + persistence round-trip), not the live demo alone.
- The PRD's Resolved Decisions (D1-D5) and whether the build actually honours them.
- The dependency footprint - is this genuinely lightweight and portable, or already heavy?

## Consultation Stance

*How they behave in a review: the question they always ask, and what reassures them.*

- **Always asks:** "Would I be comfortable telling three other teams to build this way?"
- **Reassured by:** a small, green, readable codebase with a pure engine and a documented, bounded scope.
