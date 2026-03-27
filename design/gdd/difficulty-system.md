# Difficulty System

> **Status**: Draft — 2026-03-25
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-25
> **Depends on**: Case Database GDD (City entity, `salience_score` field)

## Overview

The Difficulty System controls how challenging each daily puzzle is. Difficulty
has two independent axes:

1. **City salience** — how globally recognisable each city in a route is. A
   route through Paris → Oslo → Tokyo is easier than one through Recife →
   Dushanbe → Suva. The player's ability to reason from clue to city is
   directly proportional to how well they know that city's geography, culture,
   and climate.

2. **Wrong-option count** — how many decoy cities the player must evaluate per
   move (2–4 wrong options = 3–5 total choices). Controlled by the case author
   at authoring time.

This doc covers Axis 1 (city salience) and its role in the city pool, daily
difficulty scheduling, and wrong-option selection.

---

## City Salience Score

Every city in the City Pool carries a `salience_score` — an integer from 1–10
representing how globally recognisable that city is to an educated English-speaking
adult. Higher = more recognisable = easier to deduce from partial clues.

### Data Source: Wikidata Sitelinks

The primary source for salience scoring is **Wikidata sitelink count** — the
number of Wikipedia language editions that have an article for that city. This
is a strong proxy for global familiarity: cities that millions of people
worldwide know well have articles in many languages.

**Why sitelinks over population:**
Population correlates poorly with recognisability. Chongqing (population ~30M)
scores lower than Vatican City (population ~800) because fewer people outside
China can locate or describe Chongqing. Sitelinks capture cross-cultural
salience directly.

**Source**: Wikidata SPARQL API, queried once per city during database
compilation. Not updated at runtime.

### Salience Tiers

| Tier | Salience score | Sitelink range | Example cities |
|------|---------------|----------------|----------------|
| S | 9–10 | ≥ 150 | Tokyo, Paris, New York, London, Rome |
| A | 7–8 | 80–149 | Oslo, Nairobi, Lima, Athens, Dublin |
| B | 5–6 | 40–79 | Tbilisi, Muscat, Recife, Ulaanbaatar |
| C | 3–4 | 15–39 | Iquique, Suva, Dushanbe, Asmara |
| Excluded | — | < 15 | Not in City Pool — too obscure |

**Exclusion rule:** No city with fewer than 15 Wikidata sitelinks enters the
City Pool. This is the hard floor on obscurity. The "50th-largest city in Russia"
problem is solved by this rule — such cities rarely exceed 10–20 sitelinks.

---

## City Pool

The City Pool is the master list of cities eligible for use in cases. It is a
curated subset of all world cities, filtered by the exclusion rule above.

**Target pool size:** 200–400 cities (all Tier S/A/B/C above the exclusion floor).

**Authoring implication:** Case authors select `target_city` and `wrong_options`
from the City Pool only. Cities outside the pool cannot appear in a case.

**Pool maintenance:** The pool is a static list compiled at project setup, with
manual review. It is not auto-updated at runtime. Cities may be added or removed
between content sprints by the content lead.

---

## Daily Difficulty Scheduling

Each day's case has an overall **Difficulty Rating** (Easy / Medium / Hard) set
at authoring time by the case author. The difficulty rating is derived from the
average salience score of the 5 cities in the route (starting city + 4 leg
target cities):

```
route_salience = mean(salience_score for each city in [starting_city, leg1, leg2, leg3, leg4])
```

| `route_salience` | Difficulty label |
|-----------------|-----------------|
| 8.0 – 10.0 | Easy |
| 5.5 – 7.9 | Medium |
| 3.0 – 5.4 | Hard |

**Weekly cadence (target):**

| Day | Target difficulty |
|-----|-------------------|
| Monday | Easy |
| Tuesday | Medium |
| Wednesday | Medium |
| Thursday | Hard |
| Friday | Medium |
| Saturday | Hard |
| Sunday | Easy |

This is a guide for the content pipeline, not a system-enforced constraint.
Authors should aim for this distribution across the schedule.

---

## Wrong-Option Selection and Difficulty

Wrong options should be drawn from the **same salience tier or adjacent tiers**
as the target city. This prevents trivial elimination:

- **Bad**: Target = Tier B city, wrong options = Tier S cities. The player can
  eliminate the famous cities immediately, removing all deduction.
- **Good**: Target = Tier B city, wrong options = mix of Tier A/B/C cities. The
  player must actually reason from clues.

**Rule**: At least 2 of the wrong options per leg must be within ±1 tier of the
`target_city` salience score.

This is an authoring guideline, not a runtime constraint for MVP.

---

## Formulas

### Route Salience

```
route_salience = (
  salience(starting_city) +
  salience(leg1.target_city) +
  salience(leg2.target_city) +
  salience(leg3.target_city) +
  salience(leg4.target_city)
) / 5
```

| Variable | Type | Source |
|----------|------|--------|
| `salience(city)` | float 1–10 | `City.salience_score` from City Pool |
| `route_salience` | float 1–10 | Computed at authoring time; stored in Case |

### Effective Difficulty Per Move

The effective difficulty of a single move is a product of city salience and
option count:

```
move_difficulty = (10 - target_city.salience_score) × option_multiplier
option_multiplier = 1.0 + (wrong_options.length - 2) × 0.15
```

This is an author-facing heuristic, not a player-facing value. It helps
content leads balance legs within a case so no single move is trivially easy
or unfairly hard.

**Note**: `salience_score = 10` yields `move_difficulty = 0` for this heuristic. Tier S moves (very well-known cities) should be balanced by using more wrong options (4 decoys rather than 2) to maintain deduction quality despite the low salience delta.

---

## Edge Cases

| Scenario | Expected Behavior |
|----------|-------------------|
| A city's Wikidata sitelinks change after the pool was compiled | No change at runtime. Pool is static. Re-evaluate at next content sprint. |
| Author selects a wrong option from a much higher salience tier | Authoring validator flags it as a warning (not a hard block for MVP). Author decides. |
| Route salience is at a tier boundary (e.g. exactly 5.5) | Round to the higher difficulty label (Medium). Err on the side of rewarding the player. |
| All 5 cities are Tier S (all very famous) | Valid — this is an Easy case by design. Appropriate for Monday or Sunday. |

---

## Deferred to Vertical Slice

- Personalised difficulty based on player history (e.g. harder cases after a
  streak of correct first-guess answers)
- Player-facing difficulty indicator ("Today's case: Hard")
- Salience score displayed in case authoring tooling UI
- Automated sitelink refresh job to keep pool scores current

---

## Open Questions

| Question | Owner | Status |
|----------|-------|--------|
| Should `route_salience` be stored on the Case entity, or computed on read? | Technical Director | **RESOLVED for MVP**: compute on read, do not store. A simple sum of 5 salience_score values takes under 1ms; storing adds authoring overhead with no benefit at MVP scale. |
| Who runs the Wikidata SPARQL query to populate initial salience scores? | Content Lead | Open — needs to be done before first case sprint |
| Is 200–400 cities the right pool size for launch? | Product Director | Open — 200 minimum to avoid route repetition in a 30-day window |
