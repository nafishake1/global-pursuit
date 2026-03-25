# Clue Engine

> **Status**: Designed — pending review
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-24
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
in a case, return the correct clue string.

1. The Clue Engine reads the active case from the Case Database via Game State Manager.
2. At the start of each move, it receives from Game State Manager:
   - `currentLegIndex` (integer, 0–3): which leg the player is currently on
   - `previousMoveWasCorrect` (boolean): whether the player's last city choice was correct
3. **If `currentLegIndex === 0` or `previousMoveWasCorrect === true`**: return `legs[currentLegIndex].clue_text`
4. **If `previousMoveWasCorrect === false`**: return `legs[currentLegIndex].recovery_clue_text`
5. The returned string is passed to the UI for display. The Clue Engine does not format,
   annotate, or transform the string.
6. **Previous clues are not shown.** Only the current clue is displayed. Each clue must
   be independently sufficient to support a reasoned decision without the player
   reviewing their history.

---

#### Part 2: The Signal Funnel — Specificity by Leg

Each leg has a required specificity level. Clues authored at the wrong specificity level
for their position are invalid.

| Leg | Specificity | What the clue must do | Wrong options may span |
|---|---|---|---|
| 1 | **Broad** | Narrow to a continent or major global region | Different regions |
| 2 | **Medium** | Narrow to a sub-region or country cluster within the broad region | Same region as Leg 1 |
| 3 | **Medium-Precise** | Narrow to 2–5 plausible cities in a sub-region | Same sub-region; all plausible from the clue |
| 4 | **Precise** | Narrow to 1–2 plausible cities; the Aha moment must be achievable here | Same sub-region; plausible on first read, fail under careful reasoning |

**Stand-alone sufficiency rule**: Because previous clues are not shown, each clue
must carry enough independent signal for a player to form a reasoned decision. No
clue may rely on the player remembering a previous clue to make sense of the current one.

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
| Leg 0, any | First move of the case | `legs[0].clue_text` |
| Leg N, previous correct | Player got the last city right | `legs[N].clue_text` |
| Leg N, previous wrong | Player got the last city wrong | `legs[N].recovery_clue_text` |

### Interactions with Other Systems

| System | Direction | What flows |
|---|---|---|
| **Case Database** | Clue Engine reads | `clue_text` and `recovery_clue_text` per leg of the active case |
| **Game State Manager** | Provides input to Clue Engine | `currentLegIndex` (0–3) and `previousMoveWasCorrect` (boolean) at each move |
| **City Selection** | Receives from Clue Engine | The clue string — City Selection displays it alongside the city options (contract: Clue Engine returns a plain string; City Selection owns display) |
| **Feedback System** | Receives from Clue Engine | The clue string for the next leg, revealed after a correct move as the confirmation signal |

**Interface contract with City Selection and Feedback System**:
The Clue Engine returns a single string. It does not know how the string will be
displayed. Formatting, animation, and layout are the responsibility of the
consuming system.

## Formulas

The Clue Engine performs no mathematical calculations at runtime. It is a routing
system — input state → output string. All "formulas" are authoring constraints:

### Clue Specificity Progression

```
specificity(leg_N) > specificity(leg_N-1)   for all N in 1..3
```

Each leg's clue must be strictly more specific than the previous leg's clue.
Qualitative levels: Broad < Medium < Medium-Precise < Precise.

| Variable | Values | Description |
|---|---|---|
| `specificity(leg)` | Broad / Medium / Medium-Precise / Precise | Required specificity level by position |
| `leg_N` | 0–3 | Leg index |

**Constraint**: Leg 0 = Broad, Leg 1 = Medium, Leg 2 = Medium-Precise, Leg 3 = Precise.
Authors cannot publish a Broad clue at Leg 3 or a Precise clue at Leg 0.

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
| Player is on Leg 0 and `previousMoveWasCorrect` is false (impossible state) | Serve `clue_text`; log a state error | Leg 0 has no previous move — this state should never occur; treat as first move |
| `currentLegIndex` is out of bounds (>3) | Do not serve a clue; trigger Win/Fail State resolution | The case is complete; Clue Engine responsibility ends at Leg 3 |
| Clue text is empty string | Do not display a blank clue card; show an error placeholder and log | Empty clues are authoring failures; the player must always have something to read |
| Two consecutive wrong moves | Leg N recovery clue is served again on Leg N+1 if that move was also wrong | Each leg is evaluated independently — consecutive wrong moves do not escalate or change the routing logic |

## Dependencies

| System | Direction | Nature of Dependency |
|---|---|---|
| **Case Database** | Clue Engine depends on | Source of all clue content (`clue_text`, `recovery_clue_text` per leg) |
| **Game State Manager** | Clue Engine depends on | Provides `currentLegIndex` and `previousMoveWasCorrect` at runtime *(provisional — Game State Manager GDD not yet written)* |
| **City Selection** | Depends on Clue Engine | Receives the current clue string to display alongside city options |
| **Feedback System** | Depends on Clue Engine | Receives the next leg's clue string to reveal as the confirmation signal after a correct move |

## Tuning Knobs

| Parameter | MVP Value | Safe Range | Effect of Increase | Effect of Decrease |
|---|---|---|---|---|
| Specificity levels per leg | 4 levels (Broad/Medium/Medium-Precise/Precise) | 3–5 levels | More granular authoring guidance; harder to distinguish adjacent levels | Coarser; less signal to authors about what's expected at each position |
| Required Aha moment leg position | Leg 3 or 4 (target) | Legs 2–4 acceptable | Earlier Aha moments feel more satisfying but reduce tension arc | Later-only Aha moments risk the Precise clue being too obscure |
| Wrong options (plausibility floor) | At least 1 per leg passes reasonable inference test | 1–all options | All options plausible = maximum deduction challenge; may feel unfair | No plausible wrong options = trivial elimination, no real deduction |
| Recovery clue explicitness delta | 1 inferential step simpler | 1–2 steps simpler | Easier recovery; reduces tension | Same as primary clue = no benefit to recovery path |

## Visual/Audio Requirements

The Clue Engine returns a plain string. Visual and audio requirements belong to
the UI systems that consume it.

| Event | Responsibility |
|---|---|
| Primary clue display | City Selection UI — displays clue text prominently above city options |
| Recovery clue display | City Selection UI — same display position; no visual distinction from primary clue (the player should not feel labelled as having failed) |
| Clue reveal animation | Animation System — fade/slide in; governed by Framer Motion |

## UI Requirements

The Clue Engine outputs a string. All UI decisions are owned by the consuming system.
One constraint: recovery clue text must NOT be visually distinguished from primary clue
text. Players who receive a recovery clue must not feel penalised or called out.
The help is invisible.

## Acceptance Criteria

**Runtime:**
- [ ] Given `currentLegIndex = 0`, always returns `legs[0].clue_text` regardless of `previousMoveWasCorrect`
- [ ] Given `currentLegIndex = N` and `previousMoveWasCorrect = true`, returns `legs[N].clue_text`
- [ ] Given `currentLegIndex = N` and `previousMoveWasCorrect = false`, returns `legs[N].recovery_clue_text`
- [ ] If `recovery_clue_text` is null/empty, falls back to `clue_text` and logs an error without crashing
- [ ] Returns a non-empty string for all valid leg states
- [ ] Clue Engine response time: under 5ms (pure data lookup, no computation)

**Authoring validation (case review):**
- [ ] Every case has been reviewed against all 4 Clue Validity Principles before publish
- [ ] Every leg has a `reasoning_chain` recorded (strongly required)
- [ ] Leg 0 clue is Broad; Leg 1 is Medium; Leg 2 is Medium-Precise; Leg 3 is Precise
- [ ] Every case has at least one identified Aha moment leg (Leg 2, 3, or 4)
- [ ] No clue uses a named landmark, language/alphabet, historical event, population stat, or currency name unless the conditional safety rule is satisfied and documented
- [ ] Every `recovery_clue_text` is more explicit than its corresponding `clue_text` by at least one inferential step
- [ ] Recovery clue does not visually differ from primary clue in any authored attribute

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
