# Prototype Report: Deduction Loop

**Date**: 2026-03-25
**Prototype**: `prototypes/deduction-loop/index.html`
**Built by**: Prototyper agent

---

### Hypothesis

Players reading a natural-language clue and choosing from 3–5 city options will
experience genuine deductive reasoning rather than guessing — and the result screen
emoji grid will feel worth sharing.

### Approach

Single HTML file, no framework, no server required. Two hand-authored cases following
the Signal Funnel (Broad → Medium → Medium-Precise → Precise). Implemented:
- Case brief with first clue shown before the player starts
- 4-leg game loop with correct/wrong feedback inline
- Silent recovery clue routing (no visual distinction from primary clue)
- Result screen with emoji grid + copyable share text

Shortcuts taken: hardcoded case data, no animation, no globe, no persistence,
no daily seed. Estimated build time: ~2 hours.

---

### Result

**The mechanic works.** All systems behaved as designed:

**What worked well:**
1. **Recovery clue routing is correct and invisible.** After a wrong pick on Leg 2,
   Leg 3 silently served the recovery clue ("no longer the seat of government, which
   moved to a new city on the plains") — noticeably more explicit without feeling like
   the game was holding your hand or calling out the mistake.

2. **Dead-end feedback tone is right.** "No one here has seen the suspect. A move
   wasted — but the trail isn't cold yet." Neutral, not punishing. Consistent with
   Pillar 2 (Tension Without Cruelty).

3. **Correct feedback is satisfying.** "✓ On the trail. The signal narrows." Brief,
   confirms forward momentum without over-celebrating.

4. **Result screen lands well.** 🟩🟥🟩🟩 at 75% efficiency with criminal name and
   "Copy to share" button — immediately legible as a shareable social artifact.

5. **Visual hierarchy works.** Dark card, gold label, clue in a distinct inset box,
   city buttons as clean tap targets — the reading order is natural even without
   animation: label → clue → options.

**What needs attention before production:**

1. **Leg 1 clue is borderline trivia for the Istanbul case.** "A city famous for
   straddling the boundary of two continents, where a narrow waterway connects two
   separate bodies of water" — a geography-literate player names Istanbul immediately.
   The reasoning chain works, but the Expert Trap test may fail: a geography expert
   has no need to eliminate Athens, Cairo, or Beirut. This is the most critical
   authoring risk surfaced by the prototype. *Clue needs a rewrite that distributes
   plausibility more evenly across the wrong options.*

2. **Leg 4 (Samarkand) wrong options too close to each other.** Bukhara and
   Samarkand are both ancient Silk Road cities with blue-tiled monuments. A non-expert
   choosing between them is essentially guessing. The clue differentiator
   ("monumental central plaza where three grand buildings face each other") is correct
   but requires knowing the Registan's layout — which may be too specific for
   a non-expert. *The Aha moment is there, but the wrong option design needs work.*

3. **No time feedback.** The design spec says decisions should feel snappy (<10s).
   The prototype reveals there's no signal to the player about pace — no timer,
   no visual urgency. This is intentional for MVP (Pillar 3 governs system response
   time, not player thinking time) but worth noting for the UX design pass.

4. **"Where is X headed?" label is weak.** "WHERE IS THE COURIER HEADED?" as the
   city selection label feels mechanical. Production copy should feel more tense —
   something like "Follow the trail" or just the criminal's name as context.

---

### Metrics

| Metric | Value | Notes |
|---|---|---|
| Functional playthrough achieved | ✅ Yes | Both cases playable end-to-end |
| Recovery clue fires correctly | ✅ Yes | Verified: wrong on Leg 2 → recovery served on Leg 3 |
| Correct/wrong states render correctly | ✅ Yes | Green ✓ / red ✗ with correct city revealed |
| Result screen generates correctly | ✅ Yes | Emoji grid, efficiency %, share text all accurate |
| Clues feel like deduction (self-assessment) | ⚠️ Partial | Cases 1 Leg 1 and Leg 4 are borderline; Cases 1 Legs 2-3 and Case 2 feel cleaner |
| Prototype load time | <1s | Static HTML, instant |
| Iteration count to working build | 1 | No bugs encountered |

---

### Recommendation: PROCEED

The core loop is mechanically sound and the format produces a genuine result worth
sharing. The emoji grid result is immediately legible as a social artifact. The
recovery clue system works silently and correctly. The dead-end feedback tone is right.

**The mechanic is valid. The authoring is the hard problem.**

The prototype surfaced a design risk that was already documented but is now
*viscerally clear*: writing clues that are deductive rather than trivia-adjacent is
genuinely difficult. The Istanbul Leg 1 clue works as a reasoning chain but collapses
for an expert. The Samarkand Leg 4 clue has a good Aha moment but the wrong option
plausibility isn't tight enough.

This is not a reason to stop. It's a reason to treat clue authoring as a first-class
engineering problem, not a content problem.

---

### If Proceeding

**Architecture requirements for production:**

- The clue box and city selection layout can be built as designed with Framer Motion
  transitions (clue slides in, buttons appear sequentially)
- The globe visualization is a separate technical spike — it does not affect the
  core loop, which works without it
- The share card needs native share API (Web Share API) not just clipboard copy
- Recovery clue routing (silent, no visual distinction) is confirmed correct — implement
  exactly as prototyped

**Performance targets confirmed by prototype:**
- Clue text: 1–3 sentences at 15px/1.65 line-height fits comfortably on 375px mobile
- City button count: 4 options fits cleanly; 5 would need scrolling on small screens
  (max 4 recommended for mobile, 5 only for desktop)
- Result screen: fits one viewport on mobile — no scroll needed. Good.

**Scope adjustments:**
- None. The prototype validates the design as specified.

**Estimated production effort for the core loop (excluding globe):**
- Core game state machine: 1 day
- UI components + Framer Motion transitions: 2–3 days
- Share card + Web Share API: 0.5 days
- Case authoring (14 cases): 3–5 days (the hardest part)
- **Total for playable web MVP (no globe):** ~2 weeks

**Globe is a separate track.** The core loop is fully testable and shareable without
a globe. Ship the loop first; add the globe in the following sprint.

---

### Lessons Learned

1. **Clue authoring is a design discipline, not a writing task.** It requires the
   Reasoning Chain Test to be run on every clue before it's considered done.
   The prototype makes this concrete: bad clues are immediately obvious when you
   actually play them. Assign dedicated clue review to a second person.

2. **4 options is the right count for mobile.** 5 is too many on a 375px screen
   without scrolling. The design spec says 3–5; this prototype suggests defaulting
   to 4 and reserving 5 for desktop or very specific cases.

3. **The share card is the strongest feature on the result screen.** Players will
   look at the emoji grid first. The criminal name and efficiency % are secondary.
   The copy button needs to be prominent.

4. **"Begin the Chase" on the brief screen creates the right anticipation.** Showing
   the first clue on the brief screen (before the player starts) means they arrive at
   Move 1 already thinking — exactly as designed.

5. **The recovery clue system is design-invisible.** This is the correct behaviour
   and it's validated: players who get a recovery clue will not know they got one.
   The system should stay exactly as designed.
