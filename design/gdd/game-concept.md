# Game Concept: Global Pursuit

*Created: 2026-03-24*
*Last Updated: 2026-03-25*
*Status: Approved — core loop updated 2026-03-25*

---

## Elevator Pitch

> A daily deduction game where you track a fleeing criminal across the world —
> interpreting evolving clues to choose your next destination and capture the
> target in 4 moves or fewer. Like Wordle's daily ritual, but built around
> reasoning, not recall.

---

## Core Identity

| Aspect | Detail |
|---|---|
| **Genre** | Daily puzzle / Deduction |
| **Platform** | Web (desktop + mobile browser), iOS, Android |
| **Target Audience** | Wordle-adjacent casual players who want intellectual depth |
| **Player Count** | Single-player with social sharing |
| **Session Length** | 2–5 minutes |
| **Monetization** | TBD (likely free with optional subscription/cosmetics) |
| **Estimated Scope** | Small (MVP: 2–3 months) |
| **Comparable Titles** | Wordle, MapTap.gg, Worldle, Return of the Obra Dinn (tone), Carmen Sandiego |

---

## Core Fantasy

You are the only detective sharp enough to follow the trail. While everyone
else sees noise, you see signal. The criminal is larger-than-life — a
showboating globe-trotter who leaves a trail of clues like a calling card,
always one step ahead until suddenly, theatrically, they're not. Each clue
is a thread — and you pull until the suspect has nowhere left to run.

> **"You don't need to know the world. You need to read it."**

This is the single most important sentence in the game. It answers every
player's first question ("Do I need to be good at geography?") and must be
delivered in onboarding within the first 5 seconds.

---

## Unique Hook

Like Wordle's daily ritual, AND ALSO a multi-step deductive chase where
reasoning beats recall — you track a criminal across the globe by interpreting
evolving signals, not memorizing capitals.

---

## Player Experience Analysis (MDA Framework)

### Target Aesthetics (What the player FEELS)

| Aesthetic | Priority | How We Deliver It |
|---|---|---|
| **Sensation** | 2 | Juicy animations, globe interaction, satisfying city reveal, haptic feedback |
| **Fantasy** | 3 | You are the world's best detective — sharp, fast, unstoppable |
| **Narrative** | N/A | Case brief sets context; no story progression |
| **Challenge** | 1 | Clue interpretation under uncertainty; Path Efficiency scoring |
| **Fellowship** | 4 | Share card drives comparison and conversation without spoilers |
| **Discovery** | 5 | Learning deductive frameworks about the world through play |
| **Expression** | N/A | Not a creative game |
| **Submission** | N/A | Not a relaxation game |

### Key Dynamics (Emergent player behaviors)

- Players will discuss clues with friends, debating the correct city before committing
- Competitive players will optimize for perfect paths (🟩🟩🟩🟩)
- Players will develop personal deduction frameworks across multiple cases
- Share cards will drive social comparison and referral
- Players will feel smarter as they improve — deduction skill is learnable and visible

### Core Mechanics (Systems we build)

1. **Signal Funnel** — Clues evolve from broad (region, climate, economy) to precise (near-identifiers) across 4 moves, tightening the decision space with each step
2. **Constrained Choice** — 3–5 city pins are highlighted on the globe per move; the player spins and explores to find them all. Only pinned cities are selectable — no open guessing. The act of finding the pins is part of the experience.
3. **Dead-End Design** — Wrong cities yield no information and no penalty beyond move efficiency; tension without punishment
4. **Path Efficiency Scoring** — Score is optimal moves vs actual moves; the skill layer that drives repeat play and competitive sharing
5. **Globe as Game Board** — The globe is not decoration. It IS the selection UI. Players spin it to find highlighted city pins and tap to commit. The tactile act of spinning the globe to hunt for options is a core part of the feel. Discoverability mechanic required: players must know how many pins exist and roughly where to find them (counter + edge indicators).

---

## Player Motivation Profile

### Primary Psychological Needs Served

| Need | How This Game Satisfies It | Strength |
|---|---|---|
| **Autonomy** | Player chooses which city — commitment is theirs alone | Supporting |
| **Competence** | Path Efficiency % makes skill visible and improvable over time | Core |
| **Relatedness** | Share card creates social comparison loop with no spoilers | Core |

### Player Type Appeal (Bartle Taxonomy)

- [x] **Achievers** — Path Efficiency %, perfect path 🟩🟩🟩🟩, daily streak
- [x] **Explorers** — Deductive clue system rewards understanding patterns in the world
- [x] **Socializers** — Share card is the primary growth and retention mechanic
- [ ] **Killers/Competitors** — Light; no direct PvP in MVP

### Flow State Design

- **Onboarding curve**: Case brief + first clue is self-explanatory; 30-second implicit tutorial; no explicit instructions needed
- **Difficulty scaling**: Case difficulty varies by design (not adaptive); harder cases use less obvious clue categories — mirrors MapTap's difficulty progression model
- **Feedback clarity**: Correct = strong directional clue that confirms the trail; Wrong = player flies to the wrong city, a witness there says "Haven't seen anyone matching that description," and the city is greyed out on the globe
- **Recovery from failure**: Wrong city guesses are narrative dead ends — the player flies there, hears from a witness that nobody matching the description has been seen, and the city is greyed out on the globe. No lives lost. No new clue given. Try again.

---

## Core Loop

### Confirmed Core Loop

1. **Case Brief**: CIA Director gives the player the crime and the starting city (e.g. Paris). No choice — you go there.
2. **Arrive at city**: 3 witnesses are waiting. Player taps to reveal each clue one at a time. Witness 1 is broadest, Witness 3 is most specific.
3. **Globe selection**: The correct city + 4–5 decoys are highlighted as pins on the globe. Player spins the globe to find all pins, taps their guess.
4. **Wrong guess**: Player flies to the wrong city. A witness there says "Haven't seen anyone matching that description." Wrong city gets greyed out on the globe. Player tries again.
5. **Right guess**: Flight animation to the correct city. Proceed to next round.
6. **Repeat for 4 rounds total** (after visiting Paris).
7. **Round 4**: Player arrives at the capture city. The criminal is there. Theatrical arrest sequence.

Key details:
- Starting city (Paris) is given — it is NOT one of the 4 rounds
- 4 rounds after Paris = 4 globe selections = 4 legs
- 3 witnesses per round, revealed by tapping (not all shown at once)
- Wrong guess = fly there + narrative response + try again (no lives lost, just efficiency score hit)
- No recovery clue mechanic — wrong guess just means pick again with wrong city greyed out

### Moment-to-Moment (30 seconds)
Arrive at city → tap to reveal 3 witness clues → spin globe to find highlighted pins → tap your guess → fly to that city (right or wrong) → repeat. The core action is clue interpretation: extracting inferential signal from 3 witnesses and mapping it to a city on the globe. Wrong guesses cost efficiency, not lives.

### Short-Term (2–5 minutes)
One complete case: the criminal is always one step ahead — until they're not.
The player chases a globe-trotting criminal across 4 rounds. At each stop,
they arrive just after the criminal has fled, tapping witnesses one by one to
build a picture of where they've gone next. Round 4 is the capture: the
player has finally predicted right — they arrive and the criminal is still there.

The criminal's route is not geographically constrained. Tokyo → Buenos Aires
→ Oslo → Bangkok is a valid case. The globe-trotting IS the fantasy. Players
should feel like they're chasing a ghost around the whole world — until the
moment they finally corner them.

**Narrative framing per move:**
- Correct (Rounds 1–3): "You just missed them. But they left something behind." → next round begins
- Wrong (Rounds 1–3): Player flies to wrong city. Witness says "Haven't seen anyone matching that description." City greyed out. Try again.
- Round 4 correct: "They're here. You've got them." → theatrical arrest sequence
- Round 4 wrong: "Gone again. They were one step ahead." → city greyed out, try again

### Session-Level
Single daily puzzle. Opens in under 2 seconds, plays in under 5 minutes,
produces a shareable result card. Natural stopping point is the result screen.
Re-entry hook is tomorrow's case.

### Long-Term Progression
- Daily streak counter
- Case archive (view past cases and your path)
- Efficiency rating over time ("Your average path efficiency: 78%")
- *(Open question — see Risks: what is the Day 2 hook beyond streak?)*

### Retention Hooks

- **Curiosity**: What is tomorrow's case? Daily FOMO of the ritual
- **Investment**: Streak; improving personal efficiency rating
- **Social**: Share card drives peer comparison; playing the same puzzle as friends creates natural comparison
- **Mastery**: Getting faster and more accurate at clue interpretation; chasing perfect paths

### Emotional Arc of a Session

Every 2–5 minute case should move through this emotional beat sequence.
Clue design, animation pacing, and sound must reinforce each beat:

| Move | Emotional Beat | What Delivers It |
|---|---|---|
| Case Brief | Delight + The Hunt Begins | CIA Director delivers the crime and starting city — campy, theatrical, not dry. The criminal is already larger-than-life. |
| Round 1 | On the Trail | "I think I see where they're headed" — tapping witnesses builds the picture; committing to a pin feels bold |
| Rounds 2–3 | The Chase Tightens | The criminal keeps fleeing; each witness round brings you closer; the globe shrinks; commitment feels real and fun |
| Round 4 | The Capture | You've cornered them. Theatrical, triumphant, satisfying — like a punchline landing. Win: showboating arrest. Miss: greyed-out city, try again. |
| Share screen | Pride or Rueful Amusement | "Look at my chase" / "I'll get them tomorrow" |

### The Capture Moment

The capture is the emotional payoff of every session. It is not a correct guess — it is an arrival. Round 4's correct city is where the criminal is caught. Design it accordingly.

**The sequence (in order):**

1. **The Correct Tap** — Player taps the right city pin on Round 4. Globe animates immediately — the pin lights up, a flight arc traces from the previous city to the capture city. No delay.

2. **The Arrival** — The globe spins to center the capture city. A beat. Then:

3. **The Director Line** — CIA Director delivers capture confirmation in their theatrically dramatic register:
   - "Outstanding. They had a twelve-hour head start and you caught them anyway. Case closed."
   - "I knew it. I always know. Excellent work, Agent."
   - "Every agent we have, and it takes you. Of course it does. Case closed."
   The line is short. The drama is in the brevity.

4. **The Route Reveal** — The full criminal route animates across the globe: flight arcs trace from starting city → leg 1 → leg 2 → leg 3 → capture city. Animated in sequence, not all at once. This is the theatrical "here's what just happened" beat.

5. **The Score** — Path Efficiency % revealed with appropriate weight. 100% gets a different treatment than 60%.

6. **The Share Card** — Generated immediately. Format follows NYT Games model:
   - Criminal alias + crime in one line
   - Efficiency score
   - Route represented as emoji dots/arrows (no city names — no spoilers)
   - "Global Pursuit · [date]"

**Juice requirements for the capture moment (Pillar 5):**
- The correct tap should fire haptic feedback immediately
- The Director line should have a distinct sound design signature (different from mid-game sounds)
- The route reveal animation should feel earned — slightly slower than normal game speed
- The share card reveal should have a satisfying animation (stamp, slide in, or flip)
- The whole sequence from correct tap to share card ready should not exceed 4 seconds

**What the capture moment is NOT:**
- It is not a celebration screen with confetti and balloons (wrong register — too casual)
- It is not punishing or anticlimactic (efficiency score is not the headline — the arrest is)
- It is not slow — theatricality is not the same as delay

**Wrong guess on Round 4:**
The stakes are highest on Round 4. A wrong guess here should feel different from a wrong guess on Round 1. The wrong-city witness response on Round 4 should be the most pointed version — "They were here. They are not anymore." The city greys out. The remaining pins pulse once. The player knows they're close.

---

### The "Aha" Moment — Required Design Criterion

The target peak experience: **the player connects two clues and knows the
correct city before they even look at the options.** This is the feeling
the entire system exists to produce.

Every authored case must contain at least one moment where a thoughtful
player can reach the correct answer through inference before seeing the
choices. If no such moment exists in a case, the case has failed the
deduction standard and must be revised.

---

## Game Pillars

### Pillar 1: Deduction Over Trivia
Every correct answer must be reachable through reasoning, not geographic
recall. A smart non-expert should be able to win; a geography expert with
poor reasoning should be able to lose.

*Design test*: "Does this clue reward someone who thinks carefully, or only
someone who already knows the answer? If the latter, rewrite the clue."

### Pillar 2: Tension Without Cruelty
Failure is inefficiency, not punishment. Players always finish the case.
Wrong moves waste moves, not lives. The game should never make a player
feel dumb — only less efficient than their best self.

*Design test*: "Does this mechanic make a player feel stupid? If yes, cut it."

### Pillar 3: Snappy Above All
UI and system responses resolve in under 2 seconds. Clues are readable in
under 5 seconds. The player's decision time is not capped — deduction requires
thinking, and that thinking IS the game. What must not be slow is everything
around that thinking: load times, transitions, feedback, and animations.

**Pillar 1 vs Pillar 3 hierarchy** (decided 2026-03-24): When these pillars
conflict, Pillar 1 wins at the player's cognitive processing moment. A player
may take 15–20 seconds to decide; that is acceptable and expected. Pillar 3
governs all UI/system response times, not the player's reasoning time.

*Design test*: "Is this slow because of UI/system overhead? If yes, fix it.
Is this slow because the player is thinking? If yes, that's the game working."

### Pillar 4: Clean Social Proof
The share output must make non-players curious enough to try. No spoilers.
Pure performance signal. One glance should create FOMO.

*Design test*: "Would someone who has never played this game want to try
it based on this share card alone?"

### Pillar 5: Juice Without Lag

Every correct action is rewarded immediately — visually, sonically, and haptically.
Animations, sounds, and haptics fire on input, never after. State advances without
waiting for feedback to finish; the juice plays *over* a fast game, never *instead*
of one. The emotional register is theatrical and satisfying — the dramatic reveal,
the villain caught mid-escape. Think fanfare and flair: campy, bold, fun. The juice
should feel like the punchline landing. Players should feel like a detective who just
outsmarted a showboating criminal, not a student getting a gold star.

*Design test*: "Does this feedback fire the instant the player acts? Does it feel
theatrical and purposeful — like a payoff — or generic and decorative? If an
animation were removed, would the game feel less fun — or just less busy?"

---

### Anti-Pillars (What This Game Is NOT)

- **NOT a trivia game**: Knowing capitals, currencies, and geography facts should
  not be required. Clue validity is the design's core constraint and greatest risk.
- **NOT open-world**: No free map clicking or open destination entry. Constrained
  choice IS the mechanic. Freedom would destroy the tension architecture.
- **NOT punishing**: No lives, no streak resets for missing a day, no harsh fail
  states. Tension comes from efficiency pressure, not existential stakes.
- **NOT complex**: If onboarding takes more than 30 seconds, the design is wrong.
  Immediately playable on first encounter.
- **NOT slow feedback**: Juice that delays state or blocks interaction is lag in
  disguise. Animations that make the player wait are failures of design, not
  successes of polish.

---

## Inspiration and References

| Reference | What We Take From It | What We Do Differently | Why It Matters |
|---|---|---|---|
| **Wordle** | Daily ritual, share card, emoji grid, no-spoiler social proof | Multi-step commitment mechanic; deduction over word knowledge; geography | Validates the distribution model and social loop at scale |
| **MapTap.gg** | Globe as tactile visual centerpiece; progressive difficulty scaling; player engagement with physical world interaction | Deduction-based (not knowledge-based); constrained city choices (not open clicking); binary path scoring (not proximity points) | Validates that geography games have a large casual audience; globe interactivity is a proven engagement hook |
| **Carmen Sandiego** | Chase mechanic, geography as playground, criminal pursuit fantasy | Constrained choices; efficiency scoring; no trivia requirement; daily format | Validates the core fantasy and thematic framing |
| **Return of the Obra Dinn** | Deduction under uncertainty; satisfying inference without hand-holding | Accessible in 5 minutes, no note-taking or complex logic required | Validates that pure deduction games can feel deeply satisfying to a wide audience |
| **Worldle** | Daily geography game, share card format | Not knowledge-based; multi-step narrative chase; deduction-first | Shows geography puzzles have a proven daily audience |

### Competitive Positioning

Global Pursuit occupies the currently empty quadrant on this 2-axis map:

```
                    HIGH DEDUCTION
                          │
     Global Pursuit ●     │
                          │
  Connections ●           │     ● Obra Dinn
                          │
──────────────────────────┼──────────────────────
LOW KNOWLEDGE             │             HIGH KNOWLEDGE
                          │
  Wordle ●                │     ● Worldle
                          │     ● MapTap
                          │
                    LOW DEDUCTION
```

**Carmen Sandiego positioning note**: Carmen Sandiego is the obvious reference
and players will make the comparison automatically. The risk: Carmen Sandiego
IS a trivia game, so players who remember it will expect trivia. Onboarding
must proactively reframe: *"This is not about what you know. It's about what
you can figure out."* Position Global Pursuit as Carmen Sandiego's smarter
cousin, not its reboot.

---

**Non-game inspirations**: Cold case detective work (triangulating a suspect
from incomplete evidence); international flight logistics (mental maps of
connections and routes); newspaper puzzle ritual (daily completion satisfaction);
travel journalism (the feeling of reading a place through its signals, not its name).

### MapTap Design Learnings — Decision Closed

MapTap uses proximity-based scoring (geographically closer = more points).
The team evaluated whether Global Pursuit should adopt a similar proximity/
partial-clue mechanic for wrong answers.

**Decision (2026-03-24): REJECTED.**

Proximity is geographic knowledge, not deductive reasoning. Importing it would
violate Pillar 1 (Deduction Over Trivia) and undermine the clean dead-end design.
MapTap's globe visual and progressive difficulty model are valid learnings;
its scoring model is not applicable here.

**Pillar-consistent alternative**: After a wrong move, the next clue is slightly
more explicit than it would have been after a correct move. This achieves "the
game helps you recover" without introducing geographic proximity as a signal.

---

## Target Player Profile

| Attribute | Detail |
|---|---|
| **Age range** | 22–45 |
| **Gaming experience** | Casual to mid-core; plays mobile and browser games daily |
| **Time availability** | 5 minutes during commute, lunch, or morning routine |
| **Platform preference** | Mobile browser first; shares via social media |
| **Current games** | Wordle, Connections, Strands, NYT Games suite, MapTap.gg |
| **What they're looking for** | A puzzle that rewards intelligence, not just knowledge or luck |
| **What would turn them away** | Requires trivia knowledge; punishing fail states; slow load times; complex onboarding |

---

## Technical Considerations

| Consideration | Assessment |
|---|---|
| **Recommended Stack** | React + Framer Motion + Capacitor (Technical Director recommendation, 2026-03-24) |
| **Key Technical Challenges** | Interactive globe/map component (flight paths, city selection); daily seed system; content pipeline at scale |
| **Art Style** | Clean, editorial — NYT Games meets travel poster aesthetic; globe as hero visual |
| **Art Pipeline Complexity** | Low-medium (custom 2D/globe map, iconography, motion design) |
| **Audio Needs** | Minimal — satisfying UI feedback sounds, no music required for MVP |
| **Networking** | None for MVP (client-side only; daily seed via static JSON or lightweight API) |
| **Content Volume** | 365 cases/year minimum; 3 witness clues per round (tap to reveal) + 3–5 city options per round per case |
| **Procedural Systems** | None planned; cases are hand-authored or AI-assisted with editorial review |

---

## Risks and Open Questions

### Design Risks

- **Trivia in disguise**: Clues may inadvertently require geographic knowledge to
  solve, collapsing deduction into recall. Requires a formal clue validity framework
  before any case is authored. This is the single most existential design risk.
- **Educated guessing vs. deduction**: With 3–5 constrained choices, players may
  use elimination rather than active reasoning. If wrong answers are obviously wrong,
  the skill floor disappears.
- **Day 2 retention**: Streak alone may not sustain the daily habit long-term.
  The progression hook beyond streak is an open question requiring competitive research.

### Technical Risks

- **Globe/map component complexity**: The globe is now the primary selection UI —
  players spin it to find highlighted city pins and tap to select. This requires a
  WebGL/canvas layer (Three.js or MapLibre GL) with touch drag for rotation, raycasting
  for tap detection on 3D pin positions, edge indicators for off-screen pins, and flight
  path animations. Significantly more complex than a decorative globe. Must be the
  first technical spike in Sprint 1 — this is the highest technical risk in the project.
- **Content pipeline at scale**: 365 cases/year with 4 clues each is significant
  editorial work. AI-assisted authoring pipeline needs its own design sprint before launch.

### Market Risks

- **Wordle fatigue**: The daily puzzle space is crowded (Wordle, Connections, Strands,
  Worldle, MapTap). Positioning must lead with "deduction game" not "daily puzzle" to
  differentiate clearly.
- **Clue quality variance**: If early cases feel like trivia, the game will be
  dismissed by the audience before the mechanic is understood.
- **MapTap overlap perception**: Players familiar with MapTap may initially categorize
  Global Pursuit as a geography knowledge game. Onboarding must correct this quickly.

### Scope Risks

- **Case authoring bottleneck**: Manual case creation could block content cadence
  pre-launch. AI-assisted pipeline mitigates but requires its own design sprint.

### Clue Validity Principles (CD-mandated, 2026-03-24)

These principles govern what makes a clue valid before the full Clue Engine
system GDD is authored. No case may be published without passing all four:

1. **Reasoning Chain Test**: The correct answer must be reachable through a
   chain of inferences from the clues. If you cannot write "Clue A implies X,
   X + Clue B implies Y, Y points to City Z," the case is invalid.
2. **Non-Expert Test**: Show the clues to someone who cannot name 10 world
   capitals. If they cannot follow the reasoning chain when explained, the
   clues are too knowledge-dependent.
3. **Expert Trap Test**: A geography expert should see multiple plausible
   cities from the clues, not one obvious answer. If expertise collapses the
   decision, the clues are too easy and do not test deduction.
4. **Safe Clue Categories**: Economic signals (industry type, economic tier),
   cultural signals (customs, food, architecture style), environmental signals
   (climate, terrain, vegetation), temporal signals (timezone-adjacent, seasonal
   indicators), logistical signals (travel time, connection routes).
   **Dangerous categories**: Specific landmarks, language/alphabet, historical
   events, population statistics — these collapse into trivia.

### Open Questions

1. **Clue validity framework (full)**: Formalize the principles above into the
   Clue Engine system GDD with acceptance criteria and authoring guidelines.
   *Resolve by*: `/design-system` — Clue Engine.
2. **Wrong city selection method**: Curated per case vs. algorithmic?
   *Resolve by*: prototyping both and evaluating difficulty variance across test cases.
3. **Day 2 hook**: What progression mechanic beyond streak sustains long-term habit?
   *Resolve by*: reviewing retention mechanics in comparable daily games (Duolingo, NYT Games).
4. **Criminal route structure**: CLOSED — Option A confirmed. Criminal visits 4 cities in sequence anywhere in the world (globe-spanning). Starting city is given. 4 rounds of investigation follow. Round 4 ends with the capture. Route is fixed at case-creation time.
5. **City option count variance**: 3–5 options per move (user decision: keep flexible,
   scaled by case difficulty). Define the variance rules in the Clue Engine GDD.
6. **Capture moment design**: CLOSED — The full capture sequence (correct tap → arrival → Director line → route reveal → score → share card) is now defined in "The Capture Moment" section above.

---

## MVP Definition

**Core hypothesis**: Players find the deductive clue-interpretation loop
satisfying enough to share their result and return the next day.

**Required for MVP**:
1. Case brief display with crime, last known city, and first clue
2. Globe/map visualization with city selection UI (3–5 options per move)
3. Correct/incorrect feedback with next clue reveal
4. Win/fail state with Path Efficiency % score
5. Shareable emoji result card (clipboard copy + native share API)
6. 14–30 hand-authored cases with daily seed rotation
7. Web responsive (desktop + mobile browser, sub-2s load)

**Explicitly NOT in MVP**:
- User accounts or authentication
- Leaderboard
- Mobile app (Capacitor wrap comes post-web validation)
- Case archive / replay
- Streak tracking (add in vertical slice)
- Push notifications
- Proximity/partial clue mechanic (pending design decision)

### Scope Tiers

| Tier | Content | Features | Timeline |
|---|---|---|---|
| **MVP** | 14–30 cases | Core loop + share card | 6–8 weeks |
| **Vertical Slice** | 30–60 cases | + streak, archive, basic stats | 10–12 weeks |
| **Alpha** | 90+ cases | + Capacitor iOS/Android app | 4–5 months |
| **Full Vision** | 365+ cases/year | + accounts, leaderboard, seasons | 6–9 months |

---

## Next Steps

- [x] Creative director review — APPROVED 2026-03-24
- [x] Proximity mechanic decision — REJECTED 2026-03-24
- [x] Pillar 1 vs Pillar 3 conflict hierarchy — RESOLVED 2026-03-24
- [ ] Define Clue Engine system GDD (`/design-system` — Clue Engine) — **highest priority**
- [ ] Configure tech stack (`/setup-engine` with React + Framer Motion + Capacitor)
- [ ] Decompose concept into systems (`/map-systems`)
- [ ] Prototype map/globe component + city selection (sprint 1 technical spike)
- [ ] Author 10 test cases and validate against Clue Validity Principles
- [ ] Plan first sprint (`/sprint-plan new`)
