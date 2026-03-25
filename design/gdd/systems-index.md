# Systems Index: Global Pursuit

> **Status**: Approved
> **Created**: 2026-03-24
> **Last Updated**: 2026-03-24
> **Source Concept**: design/gdd/game-concept.md

---

## Overview

Global Pursuit is a daily deductive chase game. Its mechanical scope is tight by
design: one case per day, 4 moves maximum, constrained city choices, and a shareable
result card. The systems divide into three concerns — **content** (what cases exist
and how they're authored), **gameplay** (how the player interacts with a case and
receives feedback), and **meta** (how the game retains players across days and drives
social sharing).

The critical design bottleneck is the Clue Engine: it governs the Signal Funnel
mechanic that makes the game deduction rather than trivia. Everything downstream of
it — City Selection, Feedback, Scoring — inherits its output format. The Globe
Visualization is the highest *technical* risk and should be prototyped early
regardless of GDD completion order.

All systems are client-side for MVP. No server infrastructure is required until
User Accounts and Leaderboard are designed (Full Vision tier).

---

## Systems Enumeration

| # | System Name | Category | Priority | Status | Design Doc | Depends On |
|---|---|---|---|---|---|---|
| 1 | Case Database | Core | MVP | Approved | design/gdd/case-database.md | — |
| 2 | Daily Seed System | Core | MVP | Not Started | — | Case Database |
| 3 | Game State Manager | Core | MVP | Not Started | — | Daily Seed, Case Database |
| 4 | Clue Engine | Gameplay | MVP | Designed | design/gdd/clue-engine.md | Case Database, Game State Manager |
| 5 | City Selection | Gameplay | MVP | Not Started | — | Case Database, Game State Manager, Clue Engine |
| 6 | Case Brief Display | UI | MVP | Not Started | — | Case Database, Daily Seed, Game State Manager |
| 7 | Feedback System | Gameplay | MVP | Not Started | — | City Selection, Game State Manager, Clue Engine |
| 8 | Path Efficiency Scoring | Gameplay | MVP | Not Started | — | Game State Manager |
| 9 | Globe Visualization | UI | MVP | Not Started | — | Case Database, City Selection, Feedback System |
| 10 | Win/Fail State | Gameplay | MVP | Not Started | — | Game State Manager, Path Efficiency Scoring |
| 11 | Share Card | Meta | MVP | Not Started | — | Win/Fail State, Path Efficiency Scoring |
| 12 | Onboarding | Meta | MVP | Not Started | — | All Core + Feature systems |
| 13 | Animation System | Presentation | MVP | Not Started | — | Globe Visualization, Feedback System, Win/Fail State |
| 14 | Streak System | Progression | Vertical Slice | Not Started | — | Win/Fail State (localStorage / Capacitor Preferences) |
| 15 | Case Archive | Persistence | Vertical Slice | Not Started | — | Case Database, Game State Manager (history) |
| 16 | Stats / Efficiency History | Progression | Vertical Slice | Not Started | — | Path Efficiency Scoring, Streak System |
| 17 | Audio / Haptics | Audio | Vertical Slice | Not Started | — | Feedback System, Win/Fail State |
| 18 | Push Notifications | Meta | Alpha | Not Started | — | Daily Seed System |
| 19 | User Accounts | Core | Full Vision | Not Started | — | Game State Manager (to migrate local data) |
| 20 | Leaderboard | Meta | Full Vision | Not Started | — | User Accounts, Path Efficiency Scoring |

---

## Categories

| Category | Description |
|---|---|
| **Core** | Foundation data and state systems everything depends on |
| **Gameplay** | Systems that make the game mechanically function |
| **UI** | Player-facing visual/interactive displays |
| **Progression** | Systems that drive retention across sessions |
| **Persistence** | Save state and continuity |
| **Meta** | Social, onboarding, and platform systems |
| **Presentation** | Animation and polish layers wrapping gameplay systems |
| **Audio** | Sound and haptic feedback |

---

## Priority Tiers

| Tier | Definition | Target Milestone |
|---|---|---|
| **MVP** | Required for core loop to function and share card to work | 6–8 weeks |
| **Vertical Slice** | Required for a complete, daily-ritual-quality experience | 10–12 weeks |
| **Alpha** | Platform integration and notification infrastructure | 4–5 months |
| **Full Vision** | Competitive, cross-device, account-based features | 6–9 months |

---

## Dependency Map

### Foundation Layer (no dependencies)

1. **Case Database** — pure data store; all case content (crimes, city chains, clues,
   options, correct answers) lives here; schema decisions constrain every other system

### Core Layer (depends on foundation)

1. **Daily Seed System** — depends on: Case Database (to know which cases are in pool)
2. **Game State Manager** — depends on: Daily Seed (active case), Case Database (case data)

### Feature Layer (depends on core)

1. **Clue Engine** — depends on: Case Database (clue structures), Game State Manager
   (current move, correct/wrong history for dead-end clue calibration)
2. **City Selection** — depends on: Case Database (city option pool), Game State Manager
   (current move), Clue Engine (current clue drives plausible city set)
3. **Case Brief Display** — depends on: Case Database, Daily Seed, Game State Manager
4. **Feedback System** — depends on: City Selection (player's choice), Game State
   Manager, Clue Engine (next clue on correct; more explicit clue on wrong)
5. **Path Efficiency Scoring** — depends on: Game State Manager (move history vs optimal)
6. **Globe Visualization** — depends on: Case Database (city locations), City Selection
   (highlighted options), Feedback System (flight path on correct move)
7. **Win/Fail State** — depends on: Game State Manager, Path Efficiency Scoring
8. **Share Card** — depends on: Win/Fail State, Path Efficiency Scoring

### Presentation Layer (depends on features)

1. **Onboarding** — depends on: all core and feature systems (must demonstrate them)
2. **Animation System** — depends on: Globe Visualization, Feedback System, Win/Fail State,
   Share Card (choreographs all transitions via Framer Motion)

### Polish Layer

1. **Audio / Haptics** — depends on: Feedback System, Win/Fail State
2. **Streak System** — depends on: Win/Fail State (persists to localStorage / Capacitor Preferences)
3. **Case Archive** — depends on: Case Database, Game State Manager (persisted session history)
4. **Stats / Efficiency History** — depends on: Path Efficiency Scoring, Streak System

### Full Vision Layer

1. **Push Notifications** — depends on: Daily Seed System
2. **User Accounts** — depends on: Game State Manager (to migrate local data to server)
3. **Leaderboard** — depends on: User Accounts, Path Efficiency Scoring

---

## Recommended Design Order

Design and build from top to bottom. Systems at the same layer with no shared
dependencies can be designed in parallel.

| Order | System | Priority | Layer | Est. Effort | Notes |
|---|---|---|---|---|---|
| 1 | Clue Engine | MVP | Feature* | L | *Design first despite layer — highest existential risk; sets contracts for City Selection and Feedback |
| 2 | Case Database | MVP | Foundation | S | Schema follows Clue Engine's output format |
| 3 | Daily Seed System | MVP | Foundation | S | Simple date-based algorithm |
| 4 | Game State Manager | MVP | Core | M | State shape informed by Clue Engine + Case Database schemas |
| 5 | City Selection | MVP | Feature | M | Depends on Clue Engine contract |
| 6 | Feedback System | MVP | Feature | M | Depends on Clue Engine (dead-end clue calibration) |
| 7 | Path Efficiency Scoring | MVP | Feature | S | Simple formula; low design effort |
| 8 | Case Brief Display | MVP | Feature | S | UI spec; low design effort |
| 9 | Globe Visualization | MVP | UI | L | **Technical spike in sprint 1** — canvas/WebGL risk |
| 10 | Win/Fail State | MVP | Feature | S | Detects completion, triggers result screen |
| 11 | Share Card | MVP | Meta | S | Emoji grid + clipboard + native share API |
| 12 | Onboarding | MVP | Presentation | S | First-run experience; "You don't need to know the world" |
| 13 | Animation System | MVP | Presentation | M | Framer Motion choreography across all transitions |
| 14 | Streak System | VS | Polish | S | localStorage / Capacitor Preferences |
| 15 | Audio / Haptics | VS | Polish | S | UI sound specs |
| 16 | Case Archive | VS | Persistence | M | Requires persisting session history |
| 17 | Stats / Efficiency History | VS | Progression | S | Aggregates scoring history |
| 18 | Push Notifications | Alpha | Meta | M | Platform permission + scheduling |
| 19 | User Accounts | Full Vision | Core | L | Auth + data migration |
| 20 | Leaderboard | Full Vision | Meta | L | Requires accounts |

*Effort: S = 1 session, M = 2–3 sessions, L = 4+ sessions*

**Note on design order exception**: The Clue Engine is technically in the Feature
layer (it depends on Case Database and Game State Manager), but it is listed first
because its output contracts define the schema for Case Database and state shape for
Game State Manager. Design it first, then let those foundational systems conform to
the contracts it establishes.

---

## Circular Dependencies

**None found.** The dependency graph is a clean directed acyclic graph (DAG).

The closest potential cycle: Clue Engine ↔ City Selection. City Selection depends
on Clue Engine's current clue to determine which cities are plausible. But this is
a one-way dependency — Clue Engine does not read from City Selection. Clean.

---

## High-Risk Systems

| System | Risk Type | Risk Description | Mitigation |
|---|---|---|---|
| **Clue Engine** | Design | "Deduction vs. trivia" — if clues require geographic knowledge rather than reasoning, the game's core pillar collapses | Design GDD first; validate with 10 test cases against the 4 Clue Validity Principles before any other system is built |
| **Globe Visualization** | Technical | Interactive city selection with flight path animations may exceed DOM/SVG performance limits on mobile; may require canvas/WebGL (Pixi.js or MapLibre GL) | Technical spike in sprint 1: build a map prototype with 5 cities and animated flight paths; if DOM handles it, stay there |
| **Case Database** | Scope | 365 cases/year with 4 clues each is a content authoring commitment; manual authoring could bottleneck launch | Design AI-assisted authoring pipeline in parallel with GDD; prototype the pipeline before launch |
| **City Selection** | Design | With 3–5 constrained choices, wrong answers that are obviously wrong eliminate deduction (players guess via elimination, not reasoning) | Wrong city pool must be curated to include plausible alternatives; define curation rules in the GDD |

---

## Progress Tracker

| Metric | Count |
|---|---|
| Total systems identified | 20 |
| Design docs started | 2 |
| Design docs reviewed | 1 |
| Design docs approved | 1 |
| MVP systems designed | 2 / 13 |
| Vertical Slice systems designed | 0 / 4 |

---

## Next Steps

- [x] Systems enumeration approved — 2026-03-24
- [x] Dependency map validated — 2026-03-24
- [x] Priority assignments confirmed — 2026-03-24
- [ ] Design Clue Engine GDD first (`/design-system clue-engine`) — **start here**
- [ ] Design Case Database GDD (`/design-system case-database`)
- [ ] Technical spike: Globe Visualization prototype (sprint 1)
- [ ] Run `/gate-check pre-production` when MVP systems are designed
