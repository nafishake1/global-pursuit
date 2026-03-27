# Game Concept: Global Pursuit

*Created: 2026-03-24*
*Last Updated: 2026-03-26*
*Status: Approved — Creative Director reviewed 2026-03-24*

---

## Elevator Pitch

> A daily deduction game where you track a fleeing criminal across the world —
> interpreting evolving clues to choose your next destination and capture the
> target across exactly 4 legs. Like Wordle's daily ritual, but built around
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

You are the only detective sharp enough to follow the trail. The criminal
is a showboating globe-trotter — theatrical, flamboyant, always one step
ahead — and outwitting them should feel as fun as it feels satisfying.
Each clue is a thread. You pull it. The suspect has nowhere left to run,
and they know it.

> **"You don't need to know the world. You need to read it."**

This is the single most important sentence in the game. It answers every
player's first question ("Do I need to be good at geography?") and must be
delivered in onboarding within the first 5 seconds.

**Tone**: Campy and theatrical — 90s Carmen Sandiego energy. Bold, witty,
playful. The criminal is a character, not a threat. Cases have flair. The
detective work is smart; the delivery is fun.

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
2. **Constrained Choice** — 3–5 city options per move; no open map guessing; forces elimination and commitment
3. **Dead-End Design** — Wrong cities yield no information and no penalty beyond move efficiency; tension without punishment
4. **Path Efficiency Scoring** — Score is optimal moves vs actual moves; the skill layer that drives repeat play and competitive sharing

   **Path Efficiency formula:**
   ```
   Path Efficiency % = max(0, 100 − (totalWrongGuesses × 15) − (totalExtraWitnessesRevealed × 5))
   ```
   where `totalExtraWitnessesRevealed` = sum of (witnessesRevealed − 1) across all 4 legs
   (first witness per leg is free). Guessing correctly after Witness 1 saves 2 × 5 = 10 points
   compared to revealing all 3 witnesses first.

5. **Globe Visualization** — The world is the game board; a tactile, interactive globe surface is the primary visual (informed by MapTap.gg's player engagement with globe interaction)

> **Score floor**: Per-leg scores are floored at 0. The minimum total score is 0; the maximum is 400 (4 legs × 100). A player who guesses wrong multiple times on a single leg and reveals all witnesses cannot go below 0 for that leg. Negative per-leg calculations display as 0 in the UI and contribute 0 to the session total.

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
- **Feedback clarity**: Correct = strong directional clue that confirms the trail; Wrong = neutral dead-end ("No one here has seen the suspect")
- **Recovery from failure**: No lives, no retry; fail state is a learning moment, not a punishment

---

## Core Loop

### Moment-to-Moment (30 seconds)
Read clue → evaluate 3–5 city options → commit to one → receive feedback.
The core action is clue interpretation: extracting inferential signal from
language and mapping it to geography under time pressure. This must feel
instinctive, not studious.

### Short-Term (2–5 minutes)
One complete case: exactly 4 legs per case — the route is fixed. The player always
completes all 4 legs; winning in fewer than 4 is not possible. Wrong guesses add
to inefficiency but do not skip legs. Tension peaks at move 3–4 when options narrow
and mistakes are irreversible.

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
| Case Brief | Curiosity + Orientation | Crime, city, first clue — orient, don't overwhelm |
| Move 1 | Confidence or Doubt | "I think I see the pattern" vs "I need more signal" |
| Move 2–3 | Tightening Tension | Options narrow; stakes feel real; commitment matters |
| Move 4 | Resolution | Win: satisfaction + pride. Fail: rueful recognition |
| Share screen | Pride or Rueful Amusement | "Look at my path" / "I'll get it tomorrow" |

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

### Anti-Pillars (What This Game Is NOT)

- **NOT a trivia game**: Knowing capitals, currencies, and geography facts should
  not be required. Clue validity is the design's core constraint and greatest risk.
- **NOT open-world**: No free map clicking or open destination entry. Constrained
  choice IS the mechanic. Freedom would destroy the tension architecture.
- **NOT punishing**: No lives, no streak resets for missing a day, no harsh fail
  states. Tension comes from efficiency pressure, not existential stakes.
- **NOT complex**: If onboarding takes more than 30 seconds, the design is wrong.
  Immediately playable on first encounter.

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
       Global Pursuit ●        │
      (Moderate Knowledge)     │
                               │
  Connections ●                │     ● Obra Dinn
                               │
─────────────────────┬─────────┼──────────────────────
LOW KNOWLEDGE        │ MODERATE│         HIGH KNOWLEDGE
                     │ KNOWLEDGE
                               │
  Wordle ●                     │     ● Worldle
                               │     ● MapTap
                               │
                         LOW DEDUCTION
```

> **Positioning note**: Global Pursuit sits at HIGH DEDUCTION / MODERATE KNOWLEDGE. "Moderate" here means: knowledge is not required to finish — a non-expert can always reach the correct answer through reasoning — but geography knowledge is a genuine advantage. Players who know the world score higher and experience the Aha moment earlier. This is intentional design (Pillar 1). The game is NOT positioned at LOW KNOWLEDGE because knowledge actively improves performance; it is NOT positioned at HIGH KNOWLEDGE because no factual recall is required to win.

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

~~**Pillar-consistent alternative**: After a wrong move, the next clue is slightly more explicit than it would have been after a correct move.~~ **REMOVED** — Recovery clue mechanic removed from design. Wrong city guesses do not change the clue sequence (see Clue Engine, Core Rule 6). Wrong guesses result in a narrative dead end only.

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
| **Content Volume** | 365 cases/year minimum; 4 clues + 3–5 city options per move per case |
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

- **Globe/map component complexity**: Interactive city selection with flight path
  animations may require a canvas/WebGL layer (Pixi.js or MapLibre GL) if DOM/SVG
  hits performance limits. Must be prototyped in sprint 1 as a technical spike.
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
4. **Criminal route structure**: Does the criminal have a fixed final destination known
   at case-creation time, or does the route generate dynamically per player path?
   *Resolve by*: design doc for the case generation system.
5. **City option count variance**: 3–5 options per move (user decision: keep flexible,
   scaled by case difficulty). Define the variance rules in the Clue Engine GDD.

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
