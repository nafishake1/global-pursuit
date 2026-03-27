# Clue Engine

> **Status**: Approved — reviewed 2026-03-25
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-26
> **Implements Pillar**: Pillar 1 — Deduction Over Trivia

## Overview

The Clue Engine is the system that governs what clues are, how they are structured,
and which clue is served at each moment in a case. It operates in two modes: as an
**authoring contract** (defining the rules, categories, and validity tests that every
authored clue must pass) and as a **runtime router** (reading the active case from
the Case Database and serving the appropriate witness clue based on the player's current
position and which witness they are revealing). At runtime the Clue Engine is deliberately
simple — it reads authored content, it does not generate it. Its power lies in the quality
and precision of its authoring rules: these rules are the direct implementation of Pillar 1
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

The Clue Engine has one runtime responsibility: given the player's current position in a case and which witness they are revealing, return the correct witness clue.

1. The Clue Engine reads the active case from the Case Database via Game State Manager.
2. At each witness reveal within a move, it receives:
   - `currentLegIndex` (integer, 1–4): which leg the player is currently on
   - `currentWitnessIndex` (integer, 1–3): which witness the player is revealing (1 = first tap, 3 = final tap)
3. Returns `legs[currentLegIndex].witnesses[currentWitnessIndex]` — a WitnessClue object containing `{ clue_text, signals }`.
4. The returned object is passed to the UI. The Clue Engine does not format, annotate, or transform it.
5. **All 3 witnesses are revealed before the player makes a globe selection.** The player taps to reveal each witness in sequence; the globe selection UI appears after Witness 3 is revealed (or the player can choose to guess earlier — see Tuning Knobs).
6. **Wrong city guesses do not change the clue sequence.** If the player guesses wrong, they fly to the wrong city, receive a narrative "haven't seen them" response, and return to the globe with the wrong city greyed out. No new clue is given. The Clue Engine does not re-route on wrong guesses.
7. **Previous witnesses within the current leg remain visible** (stacked above the current witness). Previous legs' witnesses are not shown.

---

#### Part 2: The Signal Funnel — Specificity by Leg and Witness

Each leg has a required specificity level. Clues authored at the wrong specificity level
for their position are invalid. Within each leg, the 3 witnesses provide their own internal
funnel from broader to more specific.

| Leg | Specificity | Witness structure | What the clue must do | Wrong options may span |
|---|---|---|---|---|
| 1 | **Broad** | W1: broadest relative to leg target; W2: medium relative to leg target; W3: medium-precise relative to leg target | Identify broad characteristics of the criminal's next city (climate, economy, culture) | Anywhere globally — different continents acceptable |

> **"Broad" means geographic scope of elimination, not vagueness of language.** A Leg 1 clue that eliminates to a continent or major region is Broad — regardless of how vivid or evocative its language is. A single sharp, recognisable detail can be Broad in scope if it narrows to a region rather than a city. Example: "she asked where to watch the harbour bridge" is Broad (narrows to Australia) but evocative — not encyclopaedic. The specificity ladder describes the geographic range of elimination, not clue length or language register. Leg 1 clues should be vivid AND broad in scope — these are not in tension.
>
> **Evocative Not Encyclopaedic** — Leg 1 example: instead of the encyclopaedic *"The suspect was last seen boarding a flight toward a cold, landlocked country with a strong industrial heritage"*, prefer the witness-voice version: *Ticket Agent says: "She asked for the window seat — said she wanted to see the mountains when they landed. Something about flying over the Alps."* Both are Broad (eliminate to a European mountain region), but the second has witness character and earns the Aha for players who know the Alps.
| 2 | **Medium** | W1: broadest relative to leg target; W2: medium relative to leg target; W3: medium-precise relative to leg target | Narrow to a recognisable cluster of cities globally matching the signal combination | Anywhere globally — wrong options plausible from the clue alone |
| 3 | **Medium-Precise** | W1: broadest relative to leg target; W2: medium relative to leg target; W3: precise relative to leg target | Narrow to 2–5 plausible cities anywhere in the world | Anywhere globally; all plausible from the clue |
| 4 | **Precise — The Capture** | W1: broadest relative to leg target; W2: medium-precise relative to leg target; W3: the Aha moment | The Aha moment. Player has enough signal to know the city before options appear. This is the arrest — make it feel like one. | Anywhere globally; plausible on first read, fail under careful reasoning |

**Witness specificity within a leg**: Witness 1 within any leg is the broadest clue for
that leg's target city. Witness 2 narrows the field. Witness 3 is the most specific clue
for that leg's target. The inter-leg progression (Leg 1 overall is broader than Leg 4
overall) still applies — each leg's 3 witnesses provide their own internal funnel on top
of the across-leg progression.

**Globe-spanning rule**: The criminal's route is not geographically constrained. Legs 1–4
may visit cities on different continents. A route from Tokyo → Buenos Aires → Oslo →
Bangkok is valid. Wrong options are never constrained to the same region as the correct
answer — they are chosen for plausibility against the clue, not geographic proximity.

**Stand-alone sufficiency rule**: Because previous legs' witnesses are not shown, each leg's
witness sequence must carry enough independent signal for a player to form a reasoned
decision. No witness clue may rely on the player remembering a previous leg's clues to
make sense of the current one.

---

#### Part 3: Clue Validity Principles (authoring-time)

All 5 principles must be satisfied before a clue is approved. Failure on any single
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

**Principle 5 — The Entertainment Test**
Every clue must serve double duty — inferential value AND witness character voice.
A clue that is factually useful but dead on the page is invalid. If the sentence reads
like a geography textbook, rewrite it as something a human witness would actually say.
Read the clue in the witness's voice. If it sounds like a report, it fails. If it sounds
like a person who actually saw something, it passes.

*Authoring test*: "Would a player screenshot this clue and send it to a friend? If yes,
publish it. If no, rewrite it."

**Principle 6 — Text/Visual Split**
Clue text carries only what the signal icons cannot express. If a signal is already
represented in the `signals[]` tag array and will be rendered as an icon, the clue
text should not redundantly describe it. The text adds nuance, narrative, and
specificity; the icons provide category-level visual shorthand. A clue that explains
what its icons already show is too long. Cut the redundant description; let the icon
carry the category.

---

**Recovery clue mechanic: REMOVED**
The 3-witness structure gives players enough information to reach the correct answer
without a recovery path. Wrong guesses result in a narrative dead end (fly to wrong
city → witness says "Haven't seen anyone matching that description" → wrong city
greyed out → try again). No additional clue is given on a wrong guess. Post-MVP
consideration: a purchasable 4th witness clue as an optional hint.

---

#### Part 5: The Aha Moment Requirement

Every case must contain at least one leg — typically Leg 3 or Leg 4 — where a
player who has been reasoning carefully can identify the correct city before
looking at the options. This is not a runtime check; it is a case authoring
acceptance criterion.

**For geography-smart players, the Aha can and should occur at Leg 1 — that is
the premium experience worth maximum points.** A player who knows the world
reads Witness 1 of Leg 1 and already knows where the criminal is headed. That
instant recognition, earned by knowledge, is the peak experience the game is
designed to reward. Legs 3–4 Aha is the safety net for players who need the
full witness funnel — not the ceiling for the best experience.

Case reviewers must:
1. Identify which leg is designed to produce the Aha moment for an average player (target: Leg 3 or Leg 4)
2. Confirm whether a geography-smart player could reach the Aha at Leg 1 (target: yes for most cases)
3. Verify that a thoughtful player can reach the correct city through inference from that leg's witnesses, before the options are revealed
4. Reject cases where no such moment exists at any leg

### States and Transitions

The Clue Engine is stateless at runtime — it does not maintain internal state.
All state (current leg, current witness) is owned by Game State Manager and
passed in at call time. The Clue Engine reads, routes, and returns.

| Input State | Condition | Output |
|---|---|---|
| Leg 1, Witness 1 | First witness of first leg | `legs[1].witnesses[1]` |
| Leg 1, Witness 2 | Player reveals second witness of first leg | `legs[1].witnesses[2]` |
| Leg 1, Witness 3 | Player reveals third witness of first leg | `legs[1].witnesses[3]` |
| Leg N, Witness W | Player reveals witness W of leg N | `legs[N].witnesses[W]` |
| Wrong city guess | No clue change — wrong city greyed out, player tries again | Globe selection state remains active |

### Interactions with Other Systems

| System | Direction | What flows |
|---|---|---|
| **Case Database** | Clue Engine reads | `witnesses[]` (3 WitnessClue objects per leg) for the active case |
| **Game State Manager** | Provides input to Clue Engine | `currentLegIndex` (1–4) and `currentWitnessIndex` (1–3). `previousMoveWasCorrect` is no longer passed to the Clue Engine — wrong guess routing is handled by the Game State Manager, not the Clue Engine. |
| **City Selection** | Receives from Clue Engine | WitnessClue object `{ clue_text, signals }` — City Selection displays it alongside the city options (contract: Clue Engine returns a WitnessClue object; City Selection owns display) |
| **Feedback System** | Receives from Clue Engine | WitnessClue object `{ clue_text, signals }` for the next leg's first witness, revealed after a correct move as the confirmation signal |

**Interface contract with City Selection and Feedback System**:
The Clue Engine returns a WitnessClue object `{ clue_text, signals }`. It does not
know how the object will be displayed. Formatting, animation, and layout are the
responsibility of the consuming system.

## Formulas

The Clue Engine performs no mathematical calculations at runtime. It is a routing
system — input state → output WitnessClue object. All "formulas" are authoring constraints:

### Clue Specificity Progression

```
specificity(leg_N) > specificity(leg_N-1)   for all N in 2..4
```

Each leg's clues must be strictly more specific than the previous leg's clues.
Within each leg, each witness must be at least as specific as the previous witness.
Qualitative levels: Broad < Medium < Medium-Precise < Precise.

| Variable | Values | Description |
|---|---|---|
| `specificity(leg)` | Broad / Medium / Medium-Precise / Precise | Required specificity level by position |
| `leg_N` | 1–4 | Leg index |

**Constraint**: Leg 1 = Broad, Leg 2 = Medium, Leg 3 = Medium-Precise, Leg 4 = at minimum Medium-Precise; Precise preferred for Leg 4.
Authors cannot publish a Broad clue at Leg 4 or a Precise clue at Leg 1.

## Edge Cases

| Scenario | Expected Behavior | Rationale |
|---|---|---|
| `witnesses[]` is null/empty for a leg | Do not serve a clue; log an authoring error | Witness array is required in MVP schema; absence is an authoring failure but must not crash the game |
| Player is on Leg 1 and `currentWitnessIndex` is out of expected range | Serve `witnesses[1]`; log a state error | Leg 1 always starts at Witness 1 — an out-of-range witness index is a state error; treat as first witness |
| `currentLegIndex` is out of bounds (>4) | Do not serve a clue; trigger Win/Fail State resolution | The case is complete; Clue Engine responsibility ends at Leg 4 |
| Clue text is empty string | Do not display a blank clue card; show an error placeholder and log | Empty clues are authoring failures; the player must always have something to read |
| Player guesses wrong city | No new clue served — wrong city is greyed out and the active witness sequence remains unchanged | Wrong guess routing is owned by Game State Manager; Clue Engine state does not change on a wrong guess |

## Dependencies

| System | Direction | Nature of Dependency |
|---|---|---|
| **Case Database** | Clue Engine depends on | Source of all clue content (`witnesses[]` — 3 WitnessClue objects per leg) |
| **Game State Manager** | Clue Engine depends on | Provides `currentLegIndex` (1–4) and `currentWitnessIndex` (1–3) at runtime *(provisional — Game State Manager GDD not yet written)* |
| **City Selection** | Depends on Clue Engine | Receives the current WitnessClue object `{ clue_text, signals }` to display alongside city options |
| **Feedback System** | Depends on Clue Engine | Receives the next leg's first witness WitnessClue object to reveal as the confirmation signal after a correct move |

## Tuning Knobs

| Parameter | MVP Value | Safe Range | Effect of Increase | Effect of Decrease |
|---|---|---|---|---|
| Specificity levels per leg | 4 levels (Broad/Medium/Medium-Precise/Precise) | 3–5 levels | More granular authoring guidance; harder to distinguish adjacent levels | Coarser; less signal to authors about what's expected at each position |
| Witnesses per leg | 3 witnesses | 2–4 witnesses | More information available; reduces tension and deduction challenge | Fewer witnesses; harder for players to reach the correct answer without additional help |
| Required Aha moment leg position | Leg 3 or 4 (target) | Legs 2–4 acceptable | Earlier Aha moments feel more satisfying but reduce tension arc | Later-only Aha moments risk the Precise clue being too obscure |
| Wrong options (plausibility floor) | At least 1 per leg passes reasonable inference test | 1–all options | All options plausible = maximum deduction challenge; may feel unfair | No plausible wrong options = trivial elimination, no real deduction |
| Player can guess before Witness 3 | Allowed (optional early guess) | Always optional | Skilled players can guess early for bragging rights; no penalty | Requiring all 3 witnesses before guessing reduces tension but increases fairness |

**Early guessing and Path Efficiency**: Guessing early improves Path Efficiency % — revealing fewer extra witnesses means fewer points deducted. A player who guesses correctly after Witness 1 saves 2 × 5 = 10 points compared to revealing all 3 witnesses first (2 extra witnesses not revealed × 5 points each). Early guessing is the primary skill expression beyond avoiding wrong answers.

## Visual/Audio Requirements

The Clue Engine returns a WitnessClue object `{ clue_text, signals }`. Visual and
audio requirements belong to the UI systems that consume it.

| Event | Responsibility |
|---|---|
| Witness clue display | City Selection UI — displays clue text and renders signal icons above city options |
| Witness stack display | City Selection UI — previously revealed witnesses within the current leg remain visible, stacked above the current witness |
| Clue reveal animation | Animation System — fade/slide in; governed by Framer Motion |

## UI Requirements

The Clue Engine outputs a WitnessClue object `{ clue_text, signals }`. All UI
decisions are owned by the consuming system. Signal icons are rendered by the UI
from the `signals[]` array; the Clue Engine does not produce formatted output.
Wrong guess feedback (greyed-out city, narrative dead-end response) is owned by
City Selection UI and the Feedback System — the Clue Engine has no UI role in
wrong guess handling.

## Acceptance Criteria

**Runtime:**
- [ ] Given `currentLegIndex = 1` and `currentWitnessIndex = 1`, always returns `legs[1].witnesses[1]`
- [ ] Given `currentWitnessIndex = N`, returns `witnesses[N].clue_text` and `witnesses[N].signals`
- [ ] Wrong city guess does not trigger a different clue — same witness sequence remains active
- [ ] If `witnesses[]` is null/empty, logs an error without crashing
- [ ] Returns a non-empty WitnessClue object for all valid leg and witness states
- [ ] Clue Engine response time: under 5ms (pure data lookup, no computation)

**Authoring validation (case review):**
- [ ] Every case has been reviewed against all 6 Clue Validity Principles (Principles 1–6: Reasoning Chain, Non-Expert, Expert Trap, Category Safety, Entertainment Test, Text/Visual Split) before publish
- [ ] Every leg has a `reasoning_chain` recorded for each witness (strongly required)
- [ ] Leg 1 clues are Broad; Leg 2 = Medium; Leg 3 = Medium-Precise; Leg 4 = at minimum Medium-Precise (Precise preferred)
- [ ] Within each leg, Witness 1 is broadest, Witness 3 is most specific
- [ ] Every case has at least one identified Aha moment leg (Leg 2, 3, or 4)
- [ ] No clue uses a named landmark, language/alphabet, historical event, population stat, or currency name unless the conditional safety rule is satisfied and documented
- [ ] No witness clue text redundantly describes what its `signals[]` icons already convey (Principle 6)

**Playtest validation (10 test cases):**
- [ ] At least 8/10 non-geography-expert playtesters can follow the reasoning chain when explained
- [ ] At least 6/10 playtesters report at least one Aha moment per session
- [ ] 0/10 playtesters describe the game as "a geography quiz"

---

#### Part 6: Voice and Register

##### 6.1 The Authoring Principle

Clues must pass the deduction test AND the entertainment test. A clue that is logically
fair but tonally flat is a failed clue. Witness voices carry the campy register — they
are the game's primary storytelling surface.

##### 6.2 Witness Archetypes

Each leg has exactly 3 witnesses. Authors assign each witness one of these archetypes
(can repeat across legs, should vary within a leg):

| Archetype | Personality | Voice Notes |
|-----------|-------------|-------------|
| Hotel Concierge | Formal, slightly gossipy, proud of their memory | Uses full sentences. Exaggerates politeness. Can't help adding a juicy detail. |
| Taxi Driver | Chatty, over-shares, mixes useful info with colour | Casual, rambling. Starts sentences with "Look," or "I'm telling you..." |
| Café Owner | Observant, wary, territorial about their establishment | Short sentences. Dramatic pauses implied. Slightly offended by the criminal's behaviour. |
| Airport Staff | Official tone with barely contained theatrical energy | Formal vocabulary. Can't resist the drama of what they witnessed. |
| Market Vendor | Colourful, vivid, everything is a performance | Sensory details. Exclamations. Compares things to other things. |
| Museum Guard | Deadpan, precise, utterly unimpressed | Flat delivery born from professional obligation. Clues sound like a security incident report read aloud — observable fact, no opinion, no personality. Single sentence preferred. |
| Bartender | Seen it all, mildly entertained by all of it | One or two sentences. The flatness carries the faintest wry note — not a joke, but the suggestion they find this slightly amusing. Never warm. Takes no sides. |

##### 6.3 Side-by-Side Examples

**Leg 1, Witness 1 (Taxi Driver) — target: Mumbai**
- ❌ DRY: "The suspect asked about flights to a coastal city with a tropical, humid climate."
- ✅ CAMPY: "She tipped me double and asked which way the ocean was. Like she already knew. People who already know don't ask — I've been doing this twenty years."

**Leg 2, Witness 2 (Café Owner) — target: Oslo**
- ❌ DRY: "The individual mentioned travelling somewhere cold and Scandinavian."
- ✅ CAMPY: "Ordered the hottest coffee I make. Drank it in one go. Said she wouldn't see sun for a while. Dramatic. Very dramatic. I liked her."

**Leg 3, Witness 3 (Hotel Concierge) — target: Buenos Aires**
- ❌ DRY: "The guest enquired about South American cities with a European architectural heritage and Spanish-speaking population."
- ✅ CAMPY: "She asked me — very politely, I'll admit — whether I'd ever been somewhere that felt like Europe but wasn't. I said I hadn't. She said she was about to find out. Checked out twenty minutes later. Left an excellent tip."

**Leg 4, Witness 1 (Airport Staff) — capture leg, target: Cape Town**
- ❌ DRY: "The suspect purchased a ticket to a coastal city in the Southern Hemisphere with a Mediterranean climate."
- ✅ CAMPY: "I've worked this desk for eleven years. Never seen someone smile like that buying a one-way ticket. She said, 'table mountain views.' Didn't even lower her voice."

##### 6.4 Authoring Rules for Voice

1. Every witness clue must read as dialogue or testimony, not as a report
2. The clue's inferential signal must be present — but delivered in character voice
3. Witness 1 in each leg may be the broadest signal but should be the most entertaining line
4. Witness 3 (the precise clue) can be more direct — the player is close; the payoff is the deduction, not the joke
5. Avoid: passive voice, "the suspect", clinical geography terms used without character filter
6. Use: first person observations ("She asked me..."), sensory details, implied subtext, exactly one piece of unneeded colour per clue

##### 6.5 Wrong-City Response Pool

When a player guesses the wrong city, they fly there and receive a witness response.
This response must:
- Feel like a live person, not a system message
- Vary — the same player should not read the same line twice in a session
- Carry the campy register — mild comedy, not punishment

**Per archetype, minimum 3 variants:**

Hotel Concierge:
1. "I'm afraid our records show no guest matching that description. I have an excellent memory for faces — occupational requirement."
2. "No, no. I'd have remembered. We had a cellist from Vienna last week — now that was interesting. Not this, though."
3. "Quite certain. And I'm rarely wrong about these things. It's a gift, really."

Taxi Driver:
1. "Nah, doesn't ring a bell. And trust me, the interesting ones I remember. This place? Nothing interesting ever happens."
2. "I'd know. I know everyone who comes through here. That one hasn't come through."
3. "Look, I've been on this route since six this morning. Nobody matching that. Try the other terminal."

Café Owner:
1. "I know every face that sits at my tables. That face has never sat at my tables."
2. "No. And I would know. I remember everyone who doesn't finish their coffee."
3. "Not here. My customers don't run. They linger. This one sounds like a runner."

Airport Staff:
1. "No record of that passenger on any departure today. And I would know — I processed every boarding pass this shift."
2. "That description doesn't match anyone I've seen come through. And I see everyone."
3. "Negative. Though whoever you're looking for — they sound like they'd be memorable. I'd have remembered."

Market Vendor:
1. "Ha! If someone like that came through my stall I would have sold them everything. No. Not here."
2. "I see a thousand faces a day. That one? Never."
3. "Wrong market, wrong continent, wrong everything. Try again."

Museum Guard:
1. "No."
2. "Checked the logs. Not here."
3. "No record of that individual on this shift. Confirmed absent."

Bartender:
1. "Haven't seen them. And I see everyone eventually."
2. "Not in here. Try somewhere with better lighting."
3. "Nope. Though I admire the confidence it takes to end up here."

---

## Open Questions

| Question | Owner | Status | Resolution |
|---|---|---|---|
| Does Game State Manager expose exactly `currentLegIndex` (1–4) and `currentWitnessIndex` (1–3), or a different interface? | Game State Manager GDD author | **OPEN** | Provisional interface assumed here; must be confirmed when Game State Manager GDD is written |
| Should the clue specificity levels (Broad/Medium/Medium-Precise/Precise) be formally tagged in the Case Database schema, or remain authoring guidelines only? | Product Director | **OPEN** | If tagged, the system can auto-validate specificity progression at authoring time. If guidelines only, validation is manual during case review. Recommend: tagging in Vertical Slice when authoring tooling is built. |
| Should the Aha moment leg be identified and tagged in the Case Database for analytics purposes? | Product Director | **DEFERRED** | Useful for measuring Aha moment occurrence in playtesting; resolve when analytics system is designed. |
| Should the Clue Engine return a plain string or a structured object? | Product Director | **CLOSED** | CLOSED — structured WitnessClue object `{ clue_text, signals[] }` adopted 2026-03-25. |
