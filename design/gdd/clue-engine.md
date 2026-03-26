# Clue Engine

> **Status**: Approved — reviewed 2026-03-25
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-25
> **Implements Pillar**: Pillar 1 — Deduction Over Trivia

## Overview

The Clue Engine is the system that governs what clues are, how they are structured,
and which clue is served at each moment in a case. It operates in two modes: as an
**authoring contract** (defining the rules, categories, and validity tests that every
authored clue must pass) and as a **runtime router** (reading the active case from
the Case Database and serving the appropriate clue based on the player's current
position and history). At runtime the Clue Engine is deliberately simple — it reads
authored content, it does not generate it. Its power lies in the quality and precision
of its authoring rules: these rules are the direct implementation of Pillar 1
(Deduction Over Trivia) and determine whether Global Pursuit is a deduction game or
a geography quiz in disguise.

## Player Fantasy

The Clue Engine exists to produce a single moment: the player reads a clue,
something clicks, and they know — not guess, but *know* — which city the criminal
is heading to before they even look at the options. This is the "Aha moment," and
the entire system is designed around it. A player who experiences this regularly
will describe Global Pursuit as a game that makes them feel smart. A player who
never experiences it will describe it as a geography quiz. The Clue Engine's
authoring rules are the mechanism that separates these two outcomes. From the
player's perspective, the Clue Engine is invisible — they only see a sentence.
What they feel is either the satisfaction of signal decoded, or the frustration
of noise they couldn't parse. The system's job is to always give them something
to decode.

## Detailed Design

### Core Rules

---

#### Part 1: Runtime Routing

The Clue Engine has one runtime responsibility: given the player's current position
in a case, return the correct clue object.

1. The Clue Engine reads the active case from the Case Database via Game State Manager.
2. At the start of each move, it receives from Game State Manager:
   - `currentLegIndex` (integer, 1–4): which leg the player is currently on
   - `previousMoveWasCorrect` (boolean): whether the player's last city choice was correct
3. **If `currentLegIndex === 1` or `previousMoveWasCorrect === true`**: return `legs[currentLegIndex].clue_text` and `legs[currentLegIndex].signals`
4. **If `previousMoveWasCorrect === false`**: return `legs[currentLegIndex].recovery_clue_text` and `legs[currentLegIndex].signals`
5. The returned object `{ clue_text, signals }` is passed to the UI for display. The Clue Engine does not format, annotate, or transform either field.
6. **Previous clues are not shown.** Only the current clue is displayed. Each clue must
   be independently sufficient to support a reasoned decision without the player
   reviewing their history.

---

#### Part 2: The Signal Funnel — Specificity by Leg

Each leg has a required specificity level. Clues authored at the wrong specificity level
for their position are invalid.

| Leg | Specificity | What the clue must do | Wrong options may span | Example clue |
|---|---|---|---|---|
| 1 | **Broad** | Narrow to a continent or major global region | Different regions | *"The suspect was last seen boarding a flight toward a cold, landlocked country with a strong industrial heritage."* |
| 2 | **Medium** | Narrow to a sub-region or country cluster within the broad region | Same region as Leg 1 | *"The trail leads to a coastal city in a temperate climate — a working port, not a tourist destination."* |
| 3 | **Medium-Precise** | Narrow to 2–5 plausible cities in a sub-region | Same sub-region; all plausible from the clue | *"A mid-sized city surrounded by forested hills, known for its shipbuilding past and deep natural harbour."* |
| 4 | **Precise** | Narrow to 1–2 plausible cities; the Aha moment must be achievable here | Same sub-region; plausible on first read, fail under careful reasoning | *"The harbour curves around a natural bay; the city sits on a peninsula with a distinctive hilltop fortress visible from the water."* |

**Stand-alone sufficiency rule**: Because previous clues are not shown, each clue
must carry enough independent signal for a player to form a reasoned decision. No
clue may rely on the player remembering a previous clue to make sense of the current one.

**Constraint**: Leg 1 = Broad, Leg 2 = Medium, Leg 3 = Medium-Precise, Leg 4 = Precise.
Authors cannot publish a Broad clue at Leg 4 or a Precise clue at Leg 1.

---

#### Part 3: Clue Validity Principles (authoring-time)

All 4 principles must be satisfied before a clue is approved. Failure on any single
principle invalidates the clue.

**Principle 1 — Reasoning Chain Test**
The correct answer must be reachable through a written inference chain:
"This clue implies X → X combined with the option set points to City Y."
If the author cannot write this chain, the clue is invalid. The `reasoning_chain`
field in the Case Database is the required record of this chain.

**Principle 2 — Non-Expert Test**
Show the clue and reasoning chain to someone who cannot name 10 world capitals.
If they cannot follow the logic when it's explained to them, the clue assumes
too much prior knowledge. Rewrite.

**Principle 3 — Expert Trap Test**
Show the clue to a geography expert. If they immediately identify the correct
city without considering the wrong options, the clue is too knowledge-dependent.
A valid clue leaves a geography expert with at least 2 plausible options that
require elimination to resolve.

**Principle 4 — Category Safety**

| Category | Status | Rule |
|---|---|---|
| Economic signals | ✅ Safe | Industry type, economic tier, trade patterns, financial activity |
| Cultural signals | ✅ Safe | Customs, food, architecture style, dress, social behavior |
| Environmental signals | ✅ Safe | Climate, terrain, vegetation, seasons, natural features |
| Temporal signals | ✅ Safe | Time-of-day context, seasonal indicators, timezone-adjacent signals |
| Logistical signals | ✅ Safe | Travel time, connection routes, infrastructure type, port/airport activity |
| Landmarks — **described** | ✅ Safe | Structural or visual description without naming: "a city with a famous curved harbour bridge." The player deduces; they do not recall. |
| Landmarks — **named** | ❌ Dangerous | Naming a landmark reveals the city to anyone who knows the landmark: "near the Sydney Harbour Bridge." |
| Language/alphabet | ❌ Dangerous | Requires knowing which countries use which scripts — geographic recall, not deduction |
| Historical events | ❌ Dangerous | Requires knowing which events happened where — factual recall |
| Population/statistics | ❌ Dangerous | Requires factual recall: "the most populous," "third-largest by volume" |
| Currency names | ❌ Dangerous | Requires knowing which countries use which currencies |

**Conditional safety rule**: A normally-dangerous category becomes safe only if the
clue provides the information needed to reach the conclusion. Example — "The suspect
was seen exchanging a currency whose symbol combines the letters H and R" is deducible
(the player reasons to the currency). "The suspect was exchanging Hryvnia" is not
(the player must already know Hryvnia is Ukrainian).

**Authoring brevity rule**: Text carries only what visuals cannot. If the UI communicates
a signal through an icon or city visual (e.g. a climate tag, terrain icon, or cultural
artifact), do not repeat that signal in clue text. A clue that describes in words what
the visual layer already shows is too long. When in doubt, cut.

---

#### Part 4: Recovery Clue Rules

`recovery_clue_text` is served when `previousMoveWasCorrect === false`. It is a
more explicit version of `clue_text` for the same leg, signaling the game is
helping the player recover.

Authoring rules for a valid recovery clue:
1. Must pass all 4 Clue Validity Principles.
2. Must cover the same inferential territory as `clue_text` with one fewer
   inferential step — reduced ambiguity, not a new signal.
3. Must NOT introduce knowledge-based signals absent from `clue_text`.
4. Must feel like the field is narrowing, not like the answer is given.
5. Must independently satisfy the stand-alone sufficiency rule (no dependency
   on the player remembering `clue_text`).

**Recovery clue invisibility**: The player is not informed they received a recovery
clue. The help is invisible by design. Players who receive a recovery clue must not
feel penalised or called out — the experience should feel like the trail sharpened,
not like the game intervened.

---

#### Part 5: The Aha Moment Requirement

Every case must contain at least one leg — typically Leg 3 or Leg 4 — where a
player who has been reasoning carefully can identify the correct city before
looking at the options. This is not a runtime check; it is a case authoring
acceptance criterion.

Case reviewers must:
1. Identify which leg is designed to produce the Aha moment
2. Verify that a thoughtful player can reach the correct city through inference
   from that clue alone, before the options are revealed
3. Reject cases where no such moment exists

### States and Transitions

The Clue Engine is stateless at runtime — it does not maintain internal state.
All state (current leg, previous move result) is owned by Game State Manager and
passed in at call time. The Clue Engine reads, routes, and returns.

| Input State | Condition | Output |
|---|---|---|
| Leg 1, any | First move of the case | `{ legs[1].clue_text, legs[1].signals }` |
| Leg N, previous correct | Player got the last city right | `{ legs[N].clue_text, legs[N].signals }` |
| Leg N, previous wrong | Player got the last city wrong | `{ legs[N].recovery_clue_text, legs[N].signals }` |

### Interactions with Other Systems

| System | Direction | What flows |
|---|---|---|
| **Case Database** | Clue Engine reads | `clue_text`, `recovery_clue_text`, and `signals` per leg of the active case |
| **Game State Manager** | Provides input to Clue Engine | `currentLegIndex` (1–4) and `previousMoveWasCorrect` (boolean) at each move |
| **City Selection** | Receives from Clue Engine | `{ clue_text, signals }` — City Selection displays clue text and renders signal icons; display and layout are City Selection's responsibility |
| **Feedback System** | Receives from Clue Engine | `{ clue_text, signals }` for the next leg, revealed after a correct move as the confirmation signal |

**Interface contract with City Selection and Feedback System**:
The Clue Engine returns `{ clue_text: string, signals: string[] }`. It does not know
how these will be displayed. Formatting, animation, icon rendering, and layout are
the responsibility of the consuming system.

## Formulas

The Clue Engine performs no mathematical calculations at runtime. It is a routing
system — input state → output object. All "formulas" are authoring constraints:

### Clue Specificity Progression

```
specificity(leg_N) > specificity(leg_N-1)   for all N in 2..4
```

Each leg's clue must be strictly more specific than the previous leg's clue.
Qualitative levels: Broad < Medium < Medium-Precise < Precise.

| Variable | Values | Description |
|---|---|---|
| `specificity(leg)` | Broad / Medium / Medium-Precise / Precise | Required specificity level by position |
| `leg_N` | 1–4 | Leg index |

**Constraint**: Leg 1 = Broad, Leg 2 = Medium, Leg 3 = Medium-Precise, Leg 4 = Precise.
Authors cannot publish a Broad clue at Leg 4 or a Precise clue at Leg 1.

### Recovery Clue Explicit Delta

```
inferential_steps(recovery_clue) = inferential_steps(clue_text) - 1
```

A recovery clue requires one fewer inferential step than its corresponding
primary clue. There is no formula — this is an authoring judgment validated
during case review.

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|---|---|---|
| `recovery_clue_text` is null/empty for a leg | Fall back to `clue_text`; log an authoring error | Recovery clue is required in MVP schema; absence is an authoring failure but must not crash the game |
| Player is on Leg 1 and `previousMoveWasCorrect` is false (impossible state) | Serve `clue_text`; log a state error | Leg 1 has no previous move — this state should never occur; treat as first move |
| `currentLegIndex` is out of bounds (>4) | Do not serve a clue; trigger Win/Fail State resolution | The case is complete; Clue Engine responsibility ends at Leg 4 |
| Clue text is empty string | Do not display a blank clue card; show an error placeholder and log | Empty clues are authoring failures; the player must always have something to read |
| Two consecutive wrong moves | Leg N recovery clue is served again on Leg N+1 if that move was also wrong | Each leg is evaluated independently — consecutive wrong moves do not escalate or change the routing logic |
| `signals` is null/empty for a leg | Serve `clue_text` with an empty signals array; no icons rendered | Signals are optional per leg — absence is valid, not an error |

## Dependencies

| System | Direction | Nature of Dependency |
|---|---|---|
| **Case Database** | Clue Engine depends on | Source of all clue content (`clue_text`, `recovery_clue_text`, `signals` per leg) |
| **Game State Manager** | Clue Engine depends on | Provides `currentLegIndex` and `previousMoveWasCorrect` at runtime *(provisional — Game State Manager GDD not yet written)* |
| **City Selection** | Depends on Clue Engine | Receives `{ clue_text, signals }` to display clue text and render signal icons |
| **Feedback System** | Depends on Clue Engine | Receives `{ clue_text, signals }` for the next leg to reveal as the confirmation signal after a correct move |

**Case Database follow-up**: The Case Database Leg schema requires a `signals` field
(string[], optional) to be added. This is a minor schema update to an approved doc —
no structural changes to Case Database design.

## Tuning Knobs

| Parameter | MVP Value | Safe Range | Effect of Increase | Effect of Decrease |
|---|---|---|---|---|
| Specificity levels per leg | 4 levels (Broad/Medium/Medium-Precise/Precise) | 3–5 levels | More granular authoring guidance; harder to distinguish adjacent levels | Coarser; less signal to authors about what's expected at each position |
| Required Aha moment leg position | Leg 3 or 4 (target) | Legs 2–4 acceptable | Earlier Aha moments feel more satisfying but reduce tension arc | Later-only Aha moments risk the Precise clue being too obscure |
| Wrong options (plausibility floor) | At least 1 per leg passes reasonable inference test | 1–all options | All options plausible = maximum deduction challenge; may feel unfair | No plausible wrong options = trivial elimination, no real deduction |
| Recovery clue explicitness delta | 1 inferential step simpler | 1–2 steps simpler | Easier recovery; reduces tension | Same as primary clue = no benefit to recovery path |
| Signals per leg | 1–3 tags | 0–5 | More icons = richer visual layer but cluttered UI | 0 = text-only; acceptable for some clues |

## Visual/Audio Requirements

The Clue Engine returns `{ clue_text, signals }`. Visual and audio requirements belong
to the UI systems that consume it.

| Event | Responsibility |
|---|---|
| Primary clue text display | City Selection UI — displays clue text prominently above city options |
| Signal icon rendering | City Selection UI — renders one icon per signal tag from the project's signal icon set |
| Recovery clue display | City Selection UI — same display position and visual treatment as primary clue; no distinction shown to player |
| Clue reveal animation | Animation System — fade/slide in; governed by Framer Motion |

## UI Requirements

The Clue Engine outputs `{ clue_text, signals }`. All UI decisions are owned by the
consuming system. Two constraints:

1. Recovery clue text must NOT be visually distinguished from primary clue text.
   Players who receive a recovery clue must not feel penalised or called out.
   The help is invisible.
2. Signal icons must be rendered from a finite, project-defined tag vocabulary.
   Unknown tag values should render nothing (no broken icon).

## Acceptance Criteria

**Runtime:**
- [ ] Given `currentLegIndex = 1`, always returns `legs[1].clue_text` and `legs[1].signals` regardless of `previousMoveWasCorrect`
- [ ] Given `currentLegIndex = N` and `previousMoveWasCorrect = true`, returns `legs[N].clue_text` and `legs[N].signals`
- [ ] Given `currentLegIndex = N` and `previousMoveWasCorrect = false`, returns `legs[N].recovery_clue_text` and `legs[N].signals`
- [ ] If `recovery_clue_text` is null/empty, falls back to `clue_text` and logs an error without crashing
- [ ] If `signals` is null/empty, returns an empty array without crashing
- [ ] Returns a non-empty `clue_text` string for all valid leg states
- [ ] Clue Engine response time: under 5ms (pure data lookup, no computation)

**Authoring validation (case review):**
- [ ] Every case has been reviewed against all 4 Clue Validity Principles before publish
- [ ] Every leg has a `reasoning_chain` recorded (strongly required)
- [ ] Leg 1 clue is Broad; Leg 2 is Medium; Leg 3 is Medium-Precise; Leg 4 is Precise
- [ ] Every case has at least one identified Aha moment leg (Leg 2, 3, or 4)
- [ ] No clue uses a named landmark, language/alphabet, historical event, population stat, or currency name unless the conditional safety rule is satisfied and documented
- [ ] Every `recovery_clue_text` is more explicit than its corresponding `clue_text` by at least one inferential step
- [ ] Recovery clue does not visually differ from primary clue in any authored attribute
- [ ] Clue text does not repeat signals already communicated by the `signals` icon layer

**Playtest validation (10 test cases):**
- [ ] At least 8/10 non-geography-expert playtesters can follow the reasoning chain when explained
- [ ] At least 6/10 playtesters report at least one Aha moment per session
- [ ] 0/10 playtesters describe the game as "a geography quiz"

## Open Questions

| Question | Owner | Status | Resolution |
|---|---|---|---|
| Does Game State Manager expose exactly `currentLegIndex` and `previousMoveWasCorrect`, or a different interface? | Game State Manager GDD author | **OPEN** | Provisional interface assumed here; must be confirmed when Game State Manager GDD is written |
| Should the clue specificity levels (Broad/Medium/Medium-Precise/Precise) be formally tagged in the Case Database schema, or remain authoring guidelines only? | Product Director | **OPEN** | If tagged, the system can auto-validate specificity progression at authoring time. If guidelines only, validation is manual during case review. Recommend: tagging in Vertical Slice when authoring tooling is built. |
| Should the Aha moment leg be identified and tagged in the Case Database for analytics purposes? | Product Director | **DEFERRED** | Useful for measuring Aha moment occurrence in playtesting; resolve when analytics system is designed. |
| Should `clue_text` be a plain string or structured data? | Product Director | **CLOSED** | Structured: `{ clue_text: string, signals: string[] }`. Plain string is insufficient once the visual/icon layer is part of the design. The `signals` array enables automatic icon rendering without bespoke UI per case. City-level visual assets (flag, landmark, artifact, cultural dress, currency) live on the City entity in the Case Database and are separate from per-leg signals. |
