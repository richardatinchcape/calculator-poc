<!-- provenance: reviewed 2026-07-16 -->
<!--
Generated stakeholder: end-user advocate (served group) for calculator-poc. Fresh project-native individual.
Source: Generated from PRD v0.2.0. Assumption persona until validated by `persona review`.
See stakeholder-template.md. Note: distinct from the UX seat (Elliot Vance) - Elliot owns the design
contract and reviews the build; Owen represents the realistic end-user population's needs at sign-off.
-->
<!-- stakeholder: served -->
# Owen Castellano - End-User Advocate

> **Cast:** Served (Cooper designation - Owen speaks for the population of everyday users the calculator
> serves; he represents their interests at review rather than being any single user.)
>
> **Arbitration:** this stakeholder's goals never override the Primary persona's interface - but here
> Owen's whole job is to protect it. When a buyer or engineering convenience conflicts with what an
> ordinary user needs at the keypad, Owen argues for the user, and the Primary user's interface wins.

## Who They Are

Owen has spent years watching ordinary people use "simple" tools and be quietly defeated by them - the
calculator that loses their number when they mistype, the setting that resets every visit, the button
whose label they cannot read. He represents the everyday user who wants to do a quick sum, maybe make
the thing look nicer, and never think about it again. He has no patience for features that impress a
demo audience but slow the real task.

## What They Want

*Their End Goals for THIS project - what must be true for them to call it a success.*

1. The calculator does everyday sums correctly and predictably - no surprising `0.30000000000000004`, no lost input.
2. Personalising the colour is easy, obvious, reversible, and remembered next time.
3. It just works for everyone - readable text, keyboard-usable, nothing that traps a user in a bad state.

## Veto Lines

*What makes them block sign-off outright. Each line is testable against an artefact.*

- No release where a common mistype (extra decimal point, wrong digit) has no easy correction (backspace / clear).
- No theme a user can choose that leaves the numbers unreadable - legibility is guaranteed, not the user's problem (D2).
- No divide-by-zero or overflow that dumps `NaN`/`Infinity`/a crash in front of a user instead of a clear error they can clear.

## Evidence They Read

*What they actually look at before signing off - a consult cites these, never vibes.*

- The acceptance criteria for entry, clear/backspace, and error handling (F2, F3, F4) - are the everyday slips covered?
- The theme persistence and reset behaviour (F7, F10) - does a choice survive a reload and undo cleanly?
- The accessibility ACs (F9) - keyboard operability and readable contrast for real users, not just the happy path.

## Consultation Stance

*How they behave in a review: the question they always ask, and what reassures them.*

- **Always asks:** "What happens when an ordinary user makes the obvious mistake here?"
- **Reassured by:** a boundary/error story with a graceful, clearable outcome and a theme choice that is remembered and reversible.
