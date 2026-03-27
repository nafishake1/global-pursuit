# Characters GDD

> **Status**: Approved — 2026-03-25
> **Last Updated**: 2026-03-26
> **Owner**: Creative Director
> **Depends on**: game-concept.md, clue-engine.md

## Overview

This document defines the recurring and structural characters of Global Pursuit: the CIA
Director who frames every case, the witness archetypes who populate every investigation
stop, and the per-case criminal whose trail the player follows. Characters are not
mechanical objects — they are the voice layer that sits on top of the Clue Engine and
makes the world feel inhabited. Every piece of copy in the game passes through one of
these voices. Consistency here is what separates a polished experience from a game that
feels assembled from parts.

**Tone**: Campy and theatrical — 90s Carmen Sandiego energy. Bold, witty, playful.
The criminal is a character, not a threat. The Director is never wrong. The witnesses
are vivid, unreliable in the best sense, and entirely themselves. Every voice is slightly
more than the situation requires — which is what makes it fun.

---

## Part 1: The CIA Director

### Role

The player's handler. Delivers every case brief and confirms every capture. The framing
voice of the entire game. The Director is the only character who addresses the player
directly — every other voice describes what they witnessed.

### Tone

Theatrically dramatic. Every case is personal. Every criminal is the most dangerous
they've ever faced. Every agent (you) is the best they have. They are never wrong —
and neither are you, until you are.

Crucially: the drama is slightly too much for the situation. A stolen painting is treated
with the gravity of a nuclear event. This is what makes it funny. The Director is not
joking — they are entirely sincere — which is what makes them camp.

### Example Lines — Case Brief

- "This criminal has outsmarted every agent we have. Until now. The trail starts in [city]."
- "We've tracked this one across four continents and lost them every time. I have a
  feeling today is different. The trail begins in [city]."
- "Interpol has been chasing this suspect for three years. Three years. I'm giving you
  one day."
- "I'll be honest with you, Agent — this one is personal. Last seen in [city]. Don't
  let them get away again."

### Example Lines — On Capture

- "Outstanding. They had a twelve-hour head start and you caught them anyway. Case closed."
- "I knew it. I always know. Excellent work, Agent."
- "Every agent we have, and it takes you. Of course it does. Case closed."
- "They underestimated us. They always underestimate us. Well done."

### Example Lines — Mid-Case (Post-MVP)

- "They're good. Possibly the best. But you're better. Probably."
- "The trail is warm, Agent. Don't let it cool."

### Authoring Rules for Director Copy

1. Always addressed to "Agent" — never the player's name.
2. Every case brief ends with the starting city, delivered as a directive.
3. Capture lines are short — the drama is in the brevity.
4. Never self-deprecating. Never casual. Always theatrical.
5. One implied thing about the criminal per brief (they're theatrical, they're clever,
   they've escaped before).

---

## Part 2: Witness Archetypes

Each investigation stop (leg) has exactly 3 witnesses. Case authors assign archetypes.
Archetypes can repeat across legs but **must vary within a single leg** — do not assign
3 café owners to the same city.

The witness voice carries the clue. Archetype consistency is what allows players to read
a witness's personality and calibrate how to weight their information. Every archetype has
a personality, a voice register, and a sample clue and wrong-city response. All copy must
pass Principle 5 of the Clue Validity Principles (the Entertainment Test) — see clue-engine.md.

---

### Hotel Concierge

**Personality**: Formal, slightly gossipy, immensely proud of their memory for faces.

**Voice**: Full sentences. Exaggerated politeness. An irresistible urge to add one
unnecessary detail.

**Sample clue**: "I'm afraid she checked out rather suddenly. Asked me — very
specifically — about the humidity in her next destination. I suggested she bring a light
jacket. She said she wouldn't need one."

**Sample wrong-city response**: "I'm afraid our records show no guest matching that
description. I have an excellent memory for faces — occupational requirement."

---

### Taxi Driver

**Personality**: Chatty, over-shares, mixes the signal with genuine human colour.

**Voice**: Casual. Starts with "Look," or "I'm telling you..." Trails off into relevant
tangents.

**Sample clue**: "Look, I've had a thousand fares this week. But her? She tipped me
double and asked me three times which direction the mountains were. Doesn't look like
the hiking type."

**Sample wrong-city response**: "Nah, doesn't ring a bell. And trust me — the
interesting ones I remember. This place? Nothing interesting ever happens here."

---

### Café Owner

**Personality**: Territorial, observant, slightly offended by anyone who doesn't
appreciate the establishment.

**Voice**: Short sentences. Everything is a statement. Mild drama about the smallest
things.

**Sample clue**: "She ordered the hottest coffee I make. Drank it in one go. Said she
wouldn't see sun for a while. Dramatic. Very dramatic. I liked her."

**Sample wrong-city response**: "I know every face that sits at my tables. That face
has never sat at my tables."

---

### Airport Staff

**Personality**: Officially composed but barely containing the drama of what they
witnessed.

**Voice**: Formal vocabulary. Present tense. Can't help adding one observation that
exceeds their job description.

**Sample clue**: "I processed her boarding pass myself. One-way. She said — and I
don't usually remember what passengers say — 'table mountain views.' Didn't lower
her voice."

**Sample wrong-city response**: "No record of that passenger on any departure today.
And I would know — I processed every boarding pass this shift."

---

### Market Vendor

**Personality**: Vivid, theatrical in their own right, treats everything as a story
worth telling.

**Voice**: Sensory details. Exclamations. Metaphors. Slightly competitive with the
criminal's drama.

**Sample clue**: "She bought nothing — can you imagine? Just walked through, smelled
every spice, asked me which of them came from the hottest place on earth. I told her.
She smiled and left."

**Sample wrong-city response**: "Ha! If someone like that came through my stall I
would have sold them everything. No. Not here."

---

### Museum Guard

**Personality**: Deadpan, precise, utterly unimpressed by anything including
international criminals.

**Voice**: The flatness is professional, not personal. They are paid to observe and
report — not to editorialize. Their clues read like a security incident report
delivered aloud: stripped of opinion, heavy on observable fact. No personality
leaks through. This is a feature, not a flaw.

**Sample clue**: "Subject approached the tidal exhibit at 14:23. Did not enter.
Exited south side. Stated intended direction: south."

**Sample wrong-city response**: "No record of that individual. Checked the logs.
Confirmed absent."

---

### Bartender

**Personality**: Seen it all. Delivers in one dry observation. Takes no sides.

**Voice**: The flatness comes from having seen every variety of human behaviour and
finding most of it mildly entertaining. Their clues have the faintest wry note —
not a joke, but the suggestion that they find this particular situation slightly
amusing. One or two sentences. Never warm. Never cold. Mildly entertained by
everything, including you.

**Sample clue**: "She asked what time the banks opened. Left before I answered.
Ordered water."

**Sample wrong-city response**: "Haven't seen them. Though whoever they are, I
appreciate the dramatic exit you're implying."

---

## Part 3: The Criminal — MVP Approach

### Structure

Different criminal each day. No recurring villain in MVP. Each case features a new
character with an alias, a crime, and a motivation style. The criminal exists entirely
through witness testimony and Director briefings — they have no direct voice and no
illustrated avatar in MVP.

### Schema (MVP)

| Field | Type | Description |
|---|---|---|
| `criminal_alias` | string | Theatrical, evocative alias the criminal would choose for themselves |
| `crime_description` | string (1–2 sentences) | Written in Director voice — what was taken/done and why it matters |
| `motivation_style` | enum | Authoring hint, never shown to player: `collector`, `thrill-seeker`, `hired`, `revenge`, `unknown` |

### What Makes a Good Criminal Alias

- **Theatrical** — sounds like something they'd choose for themselves.
- **International** — not anchored to one culture or region.
- **Implies a style** — the Velvet Ghost moves silently; the Archivist takes things
  that shouldn't exist.

### Example Case Brief Combinations

| Alias | Crime Description | Director Brief |
|---|---|---|
| The Velvet Ghost | A Rembrandt missing from a private collection in Amsterdam — taken without a single alarm triggered | "This criminal has outsmarted every agent we have. Until now. The trail starts in Amsterdam." |
| La Sombra | A prototype navigation chip — military grade, disappeared from a research facility in Seoul | "Interpol has been chasing this one for three years. Three years. I'm giving you one day. Last seen in Seoul." |
| The Archivist | A 12th-century manuscript — one of two known copies — walked out of a Lisbon archive in a briefcase | "I'll be honest with you, Agent — this one is personal. Last seen in Lisbon. Don't let them get away again." |

### Criminal Pronoun Guidance

Each case assigns a single consistent pronoun to the criminal (she/he/they). All witness testimony and Director copy within a case must use the same pronoun for the criminal throughout. Pronoun selection is set at the case level and applied across all witnesses in that case.

**Authoring rules:**
1. Every case must specify a pronoun for the criminal in the authoring schema. This is an authoring-time decision, not a runtime determination.
2. Pronoun usage must be consistent within a case — do not mix pronouns across witnesses or between witness testimony and the Director brief.
3. Vary pronouns across cases in the daily calendar to avoid the game defaulting to a single perceived identity for all criminals.
4. **MVP default: "she" — the criminal is coded female unless otherwise specified.** This is a nod to Carmen Sandiego and intentionally subverts the default assumption. Authors who want a different pronoun must explicitly specify it; "she" requires no additional notation.

### What Criminals Are NOT in MVP

- No illustrated avatar (post-MVP)
- No known backstory beyond alias + crime
- No recurring appearance across cases
- No dialogue of their own — they exist through witness testimony and Director briefings only

---

## Part 4: Voice Consistency Rules

Across all characters, these rules apply at all times. Authors must review every piece
of copy against this list before submitting a case.

1. **The Director is the only voice that addresses the player directly.** Witnesses
   speak about what they observed. The Director speaks to you.
2. **No witness ever names the criminal.** They describe behaviour, appearance,
   questions asked, mood. Never a name.
3. **No witness ever names the next city.** They describe it. The player identifies it.
4. **The criminal is always referred to by pronoun or descriptor in witness testimony**
   — "she", "he", "they", "the person", "your suspect". Never "the criminal" (too clinical)
   or their alias (too on-the-nose).
5. **Wrong-city responses are never apologetic.** The witness isn't sorry you're wrong.
   They just haven't seen anyone. Keep it dry or theatrical — never sympathetic.

---

## Dependencies

| System | Direction | Nature of Dependency |
|---|---|---|
| **game-concept.md** | Characters depends on | Tone, core fantasy, and Carmen Sandiego reference that ground all voice decisions |
| **clue-engine.md** | Characters depends on | Principle 5 (Entertainment Test) and the witness structure (3 per leg, archetype assignment) |
| **Case Database** | Depends on Characters | `criminal_alias`, `crime_description`, `motivation_style` fields in the Case schema; archetype field on each WitnessClue |

## Open Questions

| Question | Owner | Status |
|---|---|---|
| Should WitnessClue schema include an `archetype` field to enable tooling to flag repeated archetypes within a leg? | Product Director | **OPEN** — recommend adding in Vertical Slice when authoring tooling is built |
| Does the Director have a visual presence (avatar, animation) in MVP, or is it text-only? | Creative Director | **OPEN** — scope decision; text-only is safest for MVP timeline |
| Should `motivation_style` ever surface to the player post-capture as flavour (e.g. "a collector — of things that don't belong to them")? | Creative Director | **OPEN** — strong candidate for Vertical Slice as a rewards beat |
