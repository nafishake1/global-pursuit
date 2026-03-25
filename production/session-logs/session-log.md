## Archived Session State: 20260325_070618
# Session State — Global Pursuit

**Last Updated**: 2026-03-25
**Current Phase**: Pre-Production — Design

<!-- STATUS -->
Epic: Global Pursuit — Pre-Production
Feature: Core Loop Design
Task: Prototype validation
<!-- /STATUS -->

---

## Project Summary

**Game**: Global Pursuit — Daily Deductive Chase Game
**Concept**: Players track a fleeing criminal across 4 cities using evolving clues. Wordle's daily social loop + Carmen Sandiego's deduction.
**Pillars**:
1. Deduction Over Trivia
2. Constrained Choice
3. Snappy (sub-10s decisions)
4. Social by Default (shareable result)

**Tech Stack**: NOT YET DECIDED — deferred until concept was locked. Technical Director evaluation pending.
**Target Platforms**: Web (desktop + mobile browser) + iOS + Android

---

## Completed Work

| Artifact | Status | File |
|---|---|---|
| Game Concept Doc | ✅ Approved | design/gdd/game-concept.md |
| Systems Index | ✅ Approved | design/gdd/systems-index.md |
| Case Database GDD | ✅ Approved | design/gdd/case-database.md |
| Clue Engine GDD | ✅ Designed (pending review) | design/gdd/clue-engine.md |

---

## Current Task

**NEXT STEP: Prototype**

Decision made at end of last session: stop designing, start building. The question
"is the deduction loop satisfying?" must be answered before continuing GDD work.

Recommended path:
1. Author 3–5 test cases using Clue Engine rules (Reasoning Chain, Signal Funnel, Aha moment)
2. Build a lo-fi prototype: show clue → 4 city buttons → correct/wrong → next clue
3. Playtest — does the Aha moment happen? Does it feel like deduction or guessing?

---

## Key Decisions Made

- **4 moves max** per case, no retries
- **Current clue only** — previous clues not shown; each clue must be independently sufficient
- **Dead-end design** — wrong answers give no info, just waste a move
- **Constrained choice** — 3–5 city options per leg, not open map
- **Signal Funnel**: Leg 1 Broad → Leg 2 Medium → Leg 3 Medium-Precise → Leg 4 Precise
- **Recovery clue** — served silently when previous move was wrong, one fewer inferential step, no visual difference
- **MapTap learnings**: globe as visual centerpiece, progressive difficulty, proximity scoring (rejected for MVP — wrong = dead end)
- **Landmarks**: described = safe, named = dangerous (deduction vs. trivia)

---

## Open Questions

- Tech stack decision (Technical Director evaluation pending)
- Game State Manager interface (provisional: `currentLegIndex` + `previousMoveWasCorrect`)
- Should specificity levels be tagged in Case Database schema or remain guidelines only?

---

## Files Being Actively Worked On

- design/gdd/clue-engine.md — designed, needs CD/TD review before marking Approved
- Next: prototypes/ (lo-fi prototype)

---

## Systems Index Status

20 systems total. Design order priority:
1. ✅ Case Database
2. ✅ Clue Engine (pending review)
3. City Selection (next after prototype validation)
4. Game State Manager
5. Scoring Engine
... (see design/gdd/systems-index.md for full list)
---

## Archived Session State: 20260325_071727
# Session State — Global Pursuit

**Last Updated**: 2026-03-25
**Current Phase**: Pre-Production — Design + Prototype

<!-- STATUS -->
Epic: Global Pursuit — Pre-Production
Feature: Core Loop Validation
Task: Tech stack decision
<!-- /STATUS -->

---

## Project Summary

**Game**: Global Pursuit — Daily Deductive Chase Game
**Concept**: Track a fleeing criminal across 4 cities using evolving clues. Wordle's daily social loop + Carmen Sandiego's deduction.
**Pillars**: 1) Deduction Over Trivia  2) Constrained Choice  3) Snappy  4) Clean Social Proof
**Target Platforms**: Web (desktop + mobile browser) + iOS + Android

---

## Completed Work

| Artifact | Status | File |
|---|---|---|
| Game Concept Doc | ✅ Approved | design/gdd/game-concept.md |
| Systems Index | ✅ Approved | design/gdd/systems-index.md |
| Case Database GDD | ✅ Approved | design/gdd/case-database.md |
| Clue Engine GDD | ✅ Designed (pending review) | design/gdd/clue-engine.md |
| Deduction Loop Prototype | ✅ PROCEED | prototypes/deduction-loop/index.html |

---

## Prototype Findings (2026-03-25)

Full report: `prototypes/deduction-loop/REPORT.md`

- Core loop mechanically sound ✅
- Recovery clue routing: silent, correct, invisible to player ✅
- Result screen emoji grid: immediately shareable ✅
- **Key risk confirmed**: Clue authoring is hard — Istanbul Leg 1 borderline trivia for geography experts
- **4 options is the right count for mobile** (5 too many on 375px screen)
- **Globe is a separate track** — core loop works and is testable without it
- Estimated production effort for core loop (no globe): ~2 weeks

---

## Current Task

**NEXT: Tech stack decision**

Invoke technical-director agent with:
- Full game concept (web + iOS + Android, juicy, clean)
- Prototype findings (pure HTML/CSS/JS, <1s load, works perfectly)
- Constraint: Wordle-style daily game — load time is retention-critical
- Candidates: Phaser 3 + Capacitor vs React + Framer Motion + Capacitor

---

## Key Design Decisions

- 4 moves max per case, no retries
- Current clue only (previous clues not shown; each clue must stand alone)
- Dead-end design: wrong answers give no info, just waste a move
- Constrained choice: 3–5 options per leg (4 recommended for mobile)
- Signal Funnel: Leg 1 Broad → Leg 2 Medium → Leg 3 Medium-Precise → Leg 4 Precise
- Recovery clue: served silently when previous move wrong; visually identical to primary
- Landmarks: described = safe, named = dangerous
- Globe is MVP feature but a separate technical spike from the core loop

---

## Open Questions

- **Tech stack**: TD evaluation pending (React + Framer Motion + Capacitor vs Phaser 3 + Capacitor)
- Game State Manager interface (provisional: `currentLegIndex` + `previousMoveWasCorrect`)
- Should clue specificity levels be tagged in Case Database schema or remain guidelines only?

---

## Systems Index Status

20 systems total. Priority design order:
1. ✅ Case Database — Approved
2. ✅ Clue Engine — Designed, pending CD review
3. City Selection (next after TD/tech decision)
4. Game State Manager
5. Scoring Engine
(see design/gdd/systems-index.md for full list)
---

