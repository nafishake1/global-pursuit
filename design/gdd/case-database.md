# Case Database

> **Status**: Approved
> **Reviewed**: 2026-03-24 — design review CONCERNS resolved; blockers cleared
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-24
> **Implements Pillar**: Enables all pillars (foundational data layer)

## Overview

The Case Database is the foundational data store for Global Pursuit. It holds every
authored case — the crime, the criminal's identity, the 4-leg route, the per-leg
clues, the city option pools, and the recurring criminal roster. No gameplay happens
without it: every other system reads from the Case Database to know what to display,
what to validate, and what to reveal next. The database has no player-facing UI of
its own; it is pure authored content accessed by the Daily Seed System (which case
to load today), the Clue Engine (what clues to serve), City Selection (what options
to show), and Globe Visualization (where cities sit on the map). The quality of
every session is bounded by the quality of what's authored here.

## Player Fantasy

The Case Database doesn't have a player fantasy — it *enables* one. The fantasy
belongs to the Clue Engine and the cases it serves: the moment a player connects
two signals and knows the city before the options appear. From the author's
perspective, the database is the canvas. A well-authored case is a small, tight
piece of logic — a 4-step puzzle where every clue is necessary, no clue is
sufficient alone, and the answer is reachable by anyone who thinks carefully.
The player never knows the database exists. They only feel its quality.

## Detailed Design

### Core Rules

The Case Database is a static, read-only data store at runtime. It is written by
case authors and read by game systems. It does not change during a session.

**Entities:**

**Case**
- `id` (string) — unique identifier, e.g. `"case-001"`
- `case_number` (integer) — display number shown to players, e.g. `27`
- `criminal_alias` (string) — name or alias of the criminal, e.g. `"The Architect"`
- `crime_description` (string) — one or two sentences shown in the case brief
- `starting_city` (City) — the criminal's last known location; given to the player at the start; not a move
- `legs` (Leg[4]) — exactly 4 legs, ordered 1–4; represents the criminal's route
- `release_date` (date) — the calendar day this case is scheduled

**Leg** (one per move, 4 per case)
- `order` (integer, 1–4) — sequential position in the route
- `target_city` (City) — the correct city for this move
- `clue_text` (string) — the clue the player reads when deciding this move; must pass all 4 Clue Validity Principles
- `recovery_clue_text` (string) — a more explicit version of `clue_text`, shown by the Clue Engine when the player made a wrong move on the *previous* leg; must pass the same 4 Clue Validity Principles; authored alongside `clue_text`
- `wrong_options` (City[], length 2–4) — hand-curated decoys; combined with `target_city` yields 3–5 total options per move
- `reasoning_chain` (string, optional) — author note explaining inference path; never shown to player; used during validity review

**City** (referenced by Case and Leg)
- `id` (string) — system-only; not shown to player
- `display_name` (string) — **player-facing**; shown in city options, case brief, and globe labels, e.g. `"Bangkok"`
- `country` (string) — **player-facing**; shown in case brief and result screen
- `region` (string) — system-only; used for authoring plausibility checks; not shown to player, e.g. `"Southeast Asia"`
- `lat` (float) — system-only; used for globe positioning only
- `lng` (float) — system-only; used for globe positioning only

**Authoring rules:**
1. Every case must have exactly 4 legs. No variable-length routes in MVP.
2. Every leg must have between 2 and 4 wrong options (total options per move = 3–5).
3. `starting_city` is always revealed to the player at brief time — it is never a move.
4. `clue_text` for each leg must pass all 4 Clue Validity Principles before the case is approved (see Acceptance Criteria).
5. **Wrong option plausibility standard ("reasonable inference" test)**: At least one wrong option per leg must be a city that a thoughtful player could reasonably justify as correct from the available clue alone — before the full clue sequence has narrowed the field. A city passes the test if a reviewer can write a plausible (even if incorrect) inference chain from the clue to that city. A wrong option that no reasonable inference supports fails this test and must be replaced.
6. Each case's `release_date` must be unique — only one case per calendar day.
7. `reasoning_chain` is strongly recommended for every leg but not strictly required. Cases without it cannot be validated against the Reasoning Chain Test.

**Deferred to Vertical Slice (not in MVP schema):**
- `CriminalAlias` as a full entity with profiles, known patterns, and case history
- `difficulty` field
- `tags` for filtering and authoring tools

### States and Transitions

The Case Database is stateless — it does not change state at runtime. It is:
- **Authored** offline (content creation phase)
- **Loaded** at app startup or on-demand (read-only)
- **Read** by other systems throughout a session

There are no runtime state transitions. The only "state" is whether a case has
been released (release_date ≤ today) or is scheduled (release_date > today).

### Interactions with Other Systems

| System | Direction | What flows |
|---|---|---|
| **Daily Seed System** | Reads from Case Database | Queries the case whose `release_date` matches today |
| **Clue Engine** | Reads from Case Database | Reads `clue_text` and `wrong_options` for each leg of the active case |
| **City Selection** | Reads from Case Database | Reads `wrong_options` + `target_city` per leg to build the options list |
| **Case Brief Display** | Reads from Case Database | Reads `crime_description`, `criminal_alias`, `starting_city` |
| **Globe Visualization** | Reads from Case Database | Reads `lat`/`lng` and `display_name` for all cities in the active case |
| **Game State Manager** | Reads from Case Database | Loads the active case structure at session start |
| **Case Archive** *(Vertical Slice)* | Reads from Case Database | Retrieves past cases by `id` for review |

**Query contract**: The Case Database exposes one primary query at runtime:
`getCaseForDate(date: Date): Case | null`
Returns the Case whose `release_date` matches the given date, or `null` if no match.
All systems read the active case through this contract. The Daily Seed System calls it
at session start; downstream systems receive the resolved Case object from Game State Manager.

## Formulas

### Total Options Per Move

```
total_options = len(wrong_options) + 1
```

| Variable | Type | Range | Description |
|---|---|---|---|
| `wrong_options` | City[] | length 2–4 | Hand-curated decoys per leg |
| `total_options` | integer | 3–5 | What the player sees per move |

The authored `wrong_options` length determines difficulty feel per move:
- 2 wrong options (3 total) — easier; less to evaluate
- 4 wrong options (5 total) — harder; more elimination required

### Minimum Case Pool Size

```
pool_size ≥ target_launch_days
```

| Variable | Type | Description |
|---|---|---|
| `pool_size` | integer | Number of authored cases with a unique `release_date` assigned |
| `target_launch_days` | integer | Number of consecutive days the game must be playable without new content |

**Expected output**: `pool_size` must always be ≥ `target_launch_days`.

**Example**: For an MVP targeting 30 days of continuous availability, pool_size ≥ 30.
For a 1-year sustainable cadence, pool_size must grow by at least 1 per day.

The Daily Seed System uses `getCaseForDate(date)`. A null return produces a fallback
screen. Gaps in the schedule are a content authoring failure, not a system failure —
the case pipeline must stay ahead of the calendar by a buffer of at least 14 cases.

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|---|---|---|
| No case scheduled for today | Show a "No case today" fallback screen; do not crash | Calendar gaps are authoring failures; game must degrade gracefully without breaking |
| Case has fewer than 4 legs | Treat as invalid; do not serve; log authoring error | All cases must have exactly 4 legs — enforced at authoring/validation time |
| A City referenced in a Leg doesn't exist in the City list | Treat as invalid case; do not serve; log referential error | Referential integrity failure; must be caught during authoring validation before publish |
| Two cases share the same `release_date` | Serve the lower `case_number`; log a conflict warning | Deterministic resolution of authoring conflict; never player-facing |
| Player has already played today's case on this device | Game State Manager detects completion in local storage and presents the stored result without querying the Case Database | Case Database is never queried in replay scenarios; Game State Manager owns replay detection |
| `wrong_options` contains the `target_city` | Treat as invalid case; do not serve; log authoring error | A correct answer cannot appear as a decoy — authoring validation must catch this before publish |

## Dependencies

| System | Direction | Nature of Dependency |
|---|---|---|
| **Daily Seed System** | Depends on Case Database | Queries by `release_date` to find today's case |
| **Game State Manager** | Depends on Case Database | Loads the full active case structure at session start |
| **Clue Engine** | Depends on Case Database | Reads `clue_text` per leg as the source of all clue content |
| **City Selection** | Depends on Case Database | Reads `wrong_options` + `target_city` per leg to build the player's choice set |
| **Case Brief Display** | Depends on Case Database | Reads `crime_description`, `criminal_alias`, `starting_city` |
| **Globe Visualization** | Depends on Case Database | Reads `lat`/`lng` and `display_name` for all cities in the active case |
| **Case Archive** *(Vertical Slice)* | Depends on Case Database | Retrieves historical cases by `id` for archive display |
| **Case Database** | Depends on nothing | Foundation layer — zero upstream dependencies |

## Tuning Knobs

| Parameter | MVP Value | Safe Range | Effect of Increase | Effect of Decrease |
|---|---|---|---|---|
| `wrong_options` per leg | 2–4 (varies by case) | 2–4 | 5+ options slows decisions; violates Pillar 3 (Snappy) | 1 option = 50% random guess rate; deduction collapses |
| Legs per case | 4 (fixed) | 4 only in MVP | More legs = longer session; breaks 2–5 minute promise | Fewer legs = insufficient clue accumulation; Aha moment unlikely |
| Minimum case pool at launch | 30 | 14–365 | No upper limit | Below 14 risks calendar gaps in MVP window |
| Starting city revealed at brief | Always (fixed) | Fixed | N/A | Hiding starting city removes orientation and violates Pillar 3 |

## Visual/Audio Requirements

Not applicable. The Case Database has no direct visual or audio output. All
presentation is handled by the systems that read from it (Case Brief Display,
Globe Visualization, Clue Engine, etc.).

## UI Requirements

Not applicable. The Case Database has no player-facing UI. It is accessed
exclusively by other game systems at runtime.

## Acceptance Criteria

- [ ] Given a valid `release_date`, the system returns the correct case with all 4 legs populated
- [ ] A case with fewer or more than 4 legs is rejected during authoring validation and not served
- [ ] A leg where `target_city` appears in `wrong_options` is caught by validation and rejected
- [ ] A `release_date` collision serves the lower `case_number` and logs a warning
- [ ] A day with no scheduled case shows the fallback "no case" screen without crashing
- [ ] All City records referenced by a case's legs exist in the City list (referential integrity check passes)
- [ ] `total_options` per move is always 3–5 (wrong_options length is always 2–4)
- [ ] Case data loads from local JSON in under 100ms on a mid-range mobile device (reference: 2022-era Android mid-range, e.g. Samsung Galaxy A53 or equivalent — ~3GB RAM, Snapdragon 720G class)
- [ ] All structural values (leg count, option count bounds) are read from data configuration, not hardcoded in application logic

## Open Questions

| Question | Owner | Status | Resolution |
|---|---|---|---|
| Where does the Case Database live at runtime? | Technical Director | **CLOSED** | Static JSON bundled with the app for MVP. API endpoint added in Vertical Slice to enable hot-patching cases post-launch without app update. |
| How is the City list maintained? | Product Director | **CLOSED** | Fixed curated set of 200–500 cities. Open-ended list risks poor globe UX for obscure cities and makes wrong-option curation harder to control. |
| What file format and tooling for case authoring — JSON, YAML, or a headless CMS? | Technical Director | **DEFERRED** | JSON for MVP. Headless CMS (e.g. Contentful) is the target for Vertical Slice — enables non-technical case authoring and is the right long-term content pipeline solution. Resolve before VS content sprint. |
| Does `clue_text` format need to be structured (with metadata) rather than a plain string? | Clue Engine GDD author | **OPEN** | The Clue Engine GDD will define whether it needs structured clue data or whether a plain string is sufficient. Resolve during Clue Engine design. |
