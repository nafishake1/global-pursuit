# Signal Taxonomy

> **Status**: Approved — reviewed 2026-03-25
> **Author**: Product Director + Claude
> **Last Updated**: 2026-03-26
> **Implements Pillar**: Pillar 1 — Deduction Over Trivia
> **Dependencies**: Case Database (consumer of this vocabulary), Clue Engine (Principle 6 — Text/Visual Split)

## Overview

The Signal Taxonomy is the controlled vocabulary of tags that case authors use to
annotate every WitnessClue in the Case Database. Each tag in the `signals[]` array
maps to a rendered icon on the clue card, giving players a fast visual summary of the
clue's category before they read the text. The taxonomy is a complete enumeration —
case authors must use only these tags, and no tag may be used that is not defined here.
The icon designs are a separate deliverable owned by the Designer; this document defines
the vocabulary and authoring rules that govern them.

## Player Fantasy

Signals exist for one reason: scannability. A player glancing at a clue card sees two
or three icons and immediately knows the territory — cold place, coastal city, industrial
economy. The icons don't answer the question; they frame it. They let the player read the
clue text with the right mental context already loaded. Done well, signals are invisible
infrastructure — the player doesn't notice them, they just feel oriented faster. Done
poorly, they become hints that remove the deduction challenge. The Signal Taxonomy is
the mechanism that holds that line.

## Detailed Design

### Core Rules

---

#### Part 1: Purpose and Rules

**What signals are for:**

Signals are reading aids. They make clue cards scannable by providing a visual
category summary alongside the witness text. They are not answers, they are not
hints about which specific city the criminal is heading to — they are hints about
what kind of place it is. The player still has to connect type to specific city.
That connection is the deduction. Signals support it; they do not replace it.

**Where signals appear:**

- **Clue cards**: signals render as icons alongside witness text when a witness is
  revealed during a leg. This is the only place signals are shown.
- **City option pins on the globe**: signals do NOT appear on city pins. City pins
  show only the city's visual assets (flag, landmark, artifact — see Case Database).
  Keeping signals off the pins preserves the deduction: if a `coastal` icon appeared
  on every coastal city pin, the player would be eliminating by matching icons rather
  than by reasoning from clue to inference. That would convert deduction into visual
  matching. Signals stay on the clue card only.

**Authoring rules:**

1. Every WitnessClue must have at least 1 signal tag. An empty `signals[]` array is
   an authoring validation failure (see Case Database acceptance criteria).
2. Maximum 4 signal tags per WitnessClue. Beyond 4, the clue card becomes cluttered
   and the signal value of each icon diminishes. If an author needs more than 4 tags,
   the clue is trying to do too much — revise the witness text to carry more specificity
   directly, and let the signals cover the categories.
3. Witness 1 in a leg should use broader tag categories (climate, terrain). Witness 3
   may use more specific tag categories (economy, culture, infrastructure). This mirrors
   the specificity ladder defined in the Clue Engine — signals should track the
   specificity level of their clue, not exceed it.
4. Signal tags describe the TARGET city — not the city the player is currently in, and
   not a city mentioned in the witness text for any other reason. Every tag in
   `signals[]` is a claim about the criminal's destination.
5. Never combine tags that contradict each other. `coastal` + `landlocked` on the same
   WitnessClue is an authoring error. `polar` + `tropical` is an authoring error. The
   system cannot validate these at schema level — reviewers must catch them.
6. Tags are not hints about which city it is. They are hints about what kind of place
   it is. `financial-hub` does not mean "London." It means the criminal is heading
   somewhere whose economy is defined by international finance. Multiple cities satisfy
   that description. The player narrows from there.

---

#### Part 2: The Full Taxonomy

31 tags across 5 categories. Every tag requires a corresponding SVG icon — icon design
is the Designer's deliverable. This document defines the vocabulary; icon assets are
tracked separately.

---

##### CLIMATE (6 tags)

| Tag | Category | Description | Example cities | When to use | When NOT to use |
|-----|----------|-------------|----------------|-------------|-----------------|
| `cold` | Climate | Persistent cold, significant snowfall, sub-zero winters | Oslo, Moscow, Ulaanbaatar | Cities where cold is a defining characteristic of the place and would be immediately perceptible to a visitor | Cities that are merely "not tropical" — cold must be a dominant feature, not just present |
| `tropical` | Climate | Hot and humid year-round, rainforest or monsoon climate | Bangkok, Singapore, Mumbai, Lagos | When the heat-and-humidity combination is itself a distinctive signal — the air, the vegetation, the rain | Mediterranean climates or places that are merely warm — tropical requires the humidity combination |
| `arid` | Climate | Dry, desert or semi-arid, notably low rainfall | Dubai, Cairo, Lima, Phoenix | Cities with visibly dry, dusty, or desert-adjacent character — where lack of rain is a defining environmental feature | Cold dry climates — use `cold` instead. Arid implies heat-adjacent dryness |
| `temperate` | Climate | Mild, four seasons, neither extreme | London, Paris, Sydney, Cape Town (Western Cape) | As a narrowing tag in combination with terrain or economy tags, where a mild climate usefully narrows the field | As a standalone tag — temperate applies to too many cities globally to be informative alone |
| `polar` | Climate | Extreme cold, permafrost, very short summers, long darkness | Reykjavik, Murmansk, Fairbanks | Cities that are genuinely polar or sub-polar — where the extreme conditions would dominate a visitor's perception | Merely cold cities — `polar` is reserved for cities where the conditions are exceptional, not just cold |
| `humid` | Climate | High humidity as a defining characteristic independent of temperature | New Orleans, Tokyo (summer), Dhaka | When a witness might specifically comment on the quality of the air, the weight of the heat, or the moisture — even if the city is not strictly tropical | Tropical cities already tagged `tropical` — don't double-tag. `humid` is for non-tropical cities where humidity is still the dominant atmospheric characteristic |

---

##### TERRAIN (6 tags)

| Tag | Category | Description | Example cities | When to use | When NOT to use |
|-----|----------|-------------|----------------|-------------|-----------------|
| `coastal` | Terrain | On the ocean or a major sea; coastline is a defining feature of the city's identity | Rio de Janeiro, Lisbon, Sydney, Cape Town | Freely for genuinely coastal cities where the sea is a visible, characteristic presence — a visitor would notice it | Cities near a coast that are not defined by it. A city 40km inland from the sea is not `coastal` |
| `landlocked` | Terrain | Significantly inland, no coast access, surrounded by land | Prague, Zurich, Nairobi, Almaty | When landlocked status would be noticeable and inferentially useful — the absence of maritime character, the sense of being enclosed by land | Cities near navigable rivers that function as inland ports — landlocked implies no meaningful water access at all |
| `island` | Terrain | On an island, surrounded by water, island geography is relevant | Reykjavik, Havana, Singapore, Malta | Any city where island geography is a distinguishing feature — the sense of separation from a continental mass | Coastal cities on a continental coastline — `island` requires actual island status |
| `mountainous` | Terrain | In or surrounded by significant mountains; altitude is a defining characteristic | Kathmandu, Quito, Denver, La Paz | When altitude or mountain views would be immediately observable — the city sits in or against mountains | Cities with distant mountains on the horizon — `mountainous` requires the mountains to be a defining feature of the city's immediate geography |
| `river-city` | Terrain | Defined by a major river running through or adjacent to it | Cairo (Nile), Budapest (Danube), Buenos Aires (Río de la Plata), Bangkok (Chao Phraya) | When the river is a prominent, visible feature that would orient a visitor — the waterway defines the city's layout or character | Cities with minor rivers that are not defining features. The river must be significant enough that a visitor would notice and remember it |
| `delta` | Terrain | River delta or coastal lowland geography — flat, water-threaded, below or near sea level | Dhaka, New Orleans, Amsterdam | Cities where delta geography is genuinely distinctive — the flatness, the water channels, the sense of land reclaimed from water | Freely — `delta` applies to a small number of cities globally and should be used only when the delta character is what the witness would actually notice |

---

##### ECONOMY (6 tags)

| Tag | Category | Description | Example cities | When to use | When NOT to use |
|-----|----------|-------------|----------------|-------------|-----------------|
| `industrial` | Economy | Heavy manufacturing, factories, working-class industrial heritage visible in the city's character | Manchester, Osaka, Essen, Pittsburgh | Cities where industry is visible and defining — the architecture, the skyline, the feel of the place reflects an industrial economy | Cities that merely have factories. Industrial heritage must be a dominant characteristic that a visitor would notice |
| `financial-hub` | Economy | International finance, banking, business district of global significance | London, Zurich, Singapore, Dubai, Hong Kong | Cities known globally as financial centres — where the financial economy is visible in the density of towers, the pace of the city, the cosmopolitan business character | Regional financial centres — `financial-hub` implies international significance, not just "has a stock exchange" |
| `agricultural` | Economy | Surrounding region is predominantly agricultural; city serves as a regional market or processing hub | Des Moines, Chiang Mai, Córdoba | When agriculture is the dominant economic activity visible in the city's regional character — markets, processing facilities, a city that exists to serve a farming region | Cities with agricultural sectors that are not dominant. Many cities have farmers' markets; few have an economy defined by agriculture |
| `major-port` | Economy | Significant container shipping and cargo port — visibly present in the city | Rotterdam, Shanghai, Hamburg, Singapore | Cities where the port is a defining visual and economic feature — cranes, containers, and shipping activity are part of the city's identity | Cities with minor ports or ferry terminals. Major port implies the port is globally significant and visually dominant |
| `tourist` | Economy | Globally recognised tourist destination; tourism is a primary industry | Barcelona, Bali, Cancún, Kyoto | When tourism is a primary industry — the city's economy, its infrastructure, and its public face are organised around visitors | Cities that merely attract tourism. Many cities have tourists; `tourist` requires that tourism is a defining economic engine |
| `resource-extraction` | Economy | Oil, mining, or similar primary resource economy; city exists in service of resource extraction | Riyadh, Baku, Johannesburg, Perth (WA) | When the city exists primarily to support a resource industry — the infrastructure, the demographics, and the character of the place reflect extraction | Cities that process or trade resources without being defined by them. `resource-extraction` implies the city's identity is bound to the resource |

---

##### CULTURE (7 tags)

| Tag | Category | Description | Example cities | When to use | When NOT to use |
|-----|----------|-------------|----------------|-------------|-----------------|
| `east-asian` | Culture | East Asian cultural context: China, Japan, Korea, Taiwan — architecture, food, signage, social customs | Tokyo, Shanghai, Seoul, Taipei | When East Asian cultural signals would be visible and distinctive to a visitor — the built environment, the food culture, the social register | Southeast Asian cities — use `southeast-asian` instead. These are distinct cultural regions |
| `south-asian` | Culture | South Asian cultural context: India, Pakistan, Bangladesh, Sri Lanka — architecture, food, dress, urban character | Mumbai, Delhi, Colombo, Karachi | When South Asian cultural signals would be visible to a visitor — the density, the colour, the architectural character, the food | Southeast Asian cities — use `southeast-asian`. South Asian and Southeast Asian are distinct and should never be conflated |
| `southeast-asian` | Culture | Southeast Asian cultural context: Thailand, Vietnam, Indonesia, Philippines — temples, street food, tropical urban character | Bangkok, Ho Chi Minh City, Jakarta | Specifically for Southeast Asian cities — distinct from both `east-asian` and `south-asian` in architecture, food, religion, and urban feel | South or East Asian cities. These three categories are mutually exclusive; use the correct one |
| `western-european` | Culture | Northwestern or Central European cultural heritage — the urban grammar of Western Europe | Paris, Amsterdam, Vienna, Lisbon | When the European urban character is a defining feature — the street layout, the architecture, the café culture, the public life | Eastern European cities — use `eastern-european`. Lisbon and Warsaw have very different cultural registers |
| `eastern-european` | Culture | Eastern or Central European post-Soviet or Slavic cultural character — distinct from Western European urban character | Warsaw, Budapest, Prague, Kyiv | For cities with distinctly Eastern European character — the architecture, the pace, the urban atmosphere, the post-Soviet or Slavic cultural layer | Western European cities. Budapest and Paris are both European but are not interchangeable on this axis |
| `middle-eastern` | Culture | Middle Eastern or North African cultural context — Islamic architectural and cultural signals prominent | Dubai, Cairo, Amman, Casablanca | When Islamic architectural or cultural signals are prominent and would be immediately visible to a visitor — the minarets, the souks, the spatial organisation of the city | Vaguely "eastern" cities that don't belong to the MENA cultural region. This tag is for a specific and identifiable cultural context |
| `latin-american` | Culture | Latin American cultural context: Spanish or Portuguese colonial heritage, Latin culture, the distinctive urban and social character of the region | Buenos Aires, Mexico City, Lima, Bogotá | When the Latin American character is visually and culturally dominant — the colonial architecture, the plaza culture, the colour, the social energy | Spain or Portugal — colonial heritage does not run backwards. This tag applies to cities in Latin America, not to the Iberian Peninsula |
| `sub-saharan-african` | Culture | Sub-Saharan African cultural context: the vibrant, diverse urban character of cities south of the Sahara — distinct from North African / MENA culture; encompasses West, East, Central, and Southern African city character where African-rooted culture is dominant | Lagos, Nairobi, Cape Town, Accra | When the sub-Saharan African cultural character is visually prominent and inferentially useful — the market culture, the architectural mix of colonial legacy and modern African urbanism, the street-level energy, the cultural dress and music presence | North African cities — use `middle-eastern` for Cairo, Casablanca, Tunis. `sub-saharan-african` applies south of the Sahara only. Do not use as a catch-all for the African continent. Cape Town may warrant both `sub-saharan-african` and `temperate` + `coastal` — tag combinations reflect real cities |

---

##### INFRASTRUCTURE (5 tags)

| Tag | Category | Description | Example cities | When to use | When NOT to use |
|-----|----------|-------------|----------------|-------------|-----------------|
| `major-airport` | Infrastructure | One of the world's significant international aviation hubs — flight connections are a defining feature of the city's global role | Dubai (DXB), London (LHR), Singapore (SIN), Frankfurt (FRA) | When the airport is world-renowned or when flight connections are so central to the city's identity that a witness would naturally reference them | Cities with airports that are merely adequate. `major-airport` implies a globally significant hub — the kind of airport a traveller mentions by name |
| `rail-hub` | Infrastructure | Significant railway intersection; the city is known for its train station or rail network | Tokyo, Paris (Gare du Nord etc.), Vienna, Zurich | Cities where rail is a primary or iconic mode of transport — where the train station is a landmark and the rail network defines how people move | Cities with functional but unremarkable rail connections. `rail-hub` implies the rail infrastructure is notable enough for a witness to reference it |
| `tech-hub` | Infrastructure | Globally recognised technology and startup ecosystem | San Francisco, Seoul, Bangalore, Stockholm | When the tech economy is internationally known and visible — the cluster of campuses, the demographic, the pace, the vocabulary of the city | Cities with a technology sector that is not internationally defining. Many cities have tech companies; few have a globally recognised tech identity |
| `historic-capital` | Infrastructure | Long-established national capital with significant historical architecture — the combination of capital status and deep history | Rome, Athens, Istanbul, Beijing | When the combination of capital status and historic character is notable — the monuments, the age of the buildings, the sense of a city that has been a centre of power for centuries | New or recently established capitals, or historic cities that are not capitals. Both conditions should be substantially true |
| `trade-crossroads` | Infrastructure | Historically or currently significant as a trading crossroads between regions — geography has made this city a meeting point | Istanbul, Singapore, Muscat, Samarkand | Cities whose geographic position between regions defines their character — they have always been where things pass through, and that history is visible | Cities that trade but are not defined by their position between regions. `trade-crossroads` implies a geographic bottleneck or meeting point that has shaped the city's identity |

---

#### Part 3: Tag Combinations by Witness Position

Signals should track the specificity level of the witness clue they annotate.
Broad clues get broad tags; precise clues may use more specific tags.

| Witness | Recommended categories | Avoid |
|---------|------------------------|-------|
| Witness 1 (Broad) | climate, terrain | infrastructure tags, specific culture tags — these narrow too far too early |
| Witness 2 (Medium) | terrain, economy, broad culture tags (`western-european`, `east-asian`, etc.) | Highly specific tag combinations that reduce the field to 1–2 cities globally |
| Witness 3 (Precise) | economy, culture, infrastructure | climate tags used alone — a single `cold` tag at Witness 3 is insufficient as the only signal; combine with more specific categories |

Note: these are guidelines, not hard rules. A Witness 1 clue may include a culture tag
if the clue's text is broad enough that the culture tag does not over-narrow. Reviewers
should check that the tag combination matches the intended specificity of the clue, not
just its position in the sequence.

---

#### Part 4: Authoring Examples

Three example leg sequences demonstrating correct tag usage by witness position.

---

**Example A — Target: Oslo**

- **Witness 1** (Taxi Driver) — `cold`, `coastal`
  "She asked me which way the fjords were. Cold type — you can always tell."

- **Witness 2** (Café Owner) — `cold`, `western-european`, `financial-hub`
  "Ordered the strongest coffee. Said she needed it for the dark mornings. Dramatic. I liked her."

- **Witness 3** (Hotel Concierge) — `cold`, `western-european`, `major-airport`
  "Asked specifically about flights to Central Europe. Said Oslo had 'good connections.' Checked out before breakfast."

*Tag progression*: Witness 1 establishes cold coastal place. Witness 2 adds Western European financial character.
Witness 3 confirms a major international hub in the same cold, Western European register. Oslo becomes
reachable through elimination by Witness 3 — cold + western-european + major-airport leaves a very short list.

---

**Example B — Target: Mumbai**

- **Witness 1** (Market Vendor) — `tropical`, `coastal`
  "She smelled every spice. Asked which of them came from the hottest place on earth."

- **Witness 2** (Bartender) — `tropical`, `south-asian`, `major-port`
  "Asked about the shipping lanes. Ordered water."

- **Witness 3** (Airport Staff) — `tropical`, `south-asian`, `major-airport`
  "One-way. Said she'd 'be back when the monsoon ends.' Didn't say where she was going."

*Tag progression*: Witness 1 establishes tropical coastal. Witness 2 adds South Asian context and major port.
Witness 3 confirms a major airport in a tropical South Asian city — Mumbai, Chennai, and Kolkata remain in
contention, but the port signal from Witness 2 and the airport's scale narrow it further.

---

**Example C — Target: Buenos Aires**

- **Witness 1** (Museum Guard) — `temperate`, `coastal`
  "She asked about the estuary. Left before it opened."

- **Witness 2** (Taxi Driver) — `latin-american`, `temperate`
  "Kept asking about the architecture. European-looking but not European, she said. Look, I'm a taxi driver, not a tour guide."

- **Witness 3** (Hotel Concierge) — `latin-american`, `river-city`, `financial-hub`
  "She asked about the financial district specifically. Said it reminded her of somewhere else. Checked out at midnight."

*Tag progression*: Witness 1 establishes temperate coastal. Witness 2 adds Latin American cultural character —
European-feeling but not European. Witness 3 adds river city and financial hub, which combined with the
Latin American tag and the European-adjacent aesthetic points strongly to Buenos Aires.

---

#### Part 5: What Is Not a Signal Tag

Tags considered and rejected during taxonomy design. These are documented to prevent
them from re-entering the vocabulary.

| Rejected tag | Reason |
|--------------|--------|
| ~~`english-speaking`~~ | Violates Pillar 1 — knowing which countries are English-speaking is geographic recall, not deduction. Players either know it or they don't; there is no inference chain. |
| ~~`capital-city`~~ | Too directly narrows to one city per country. Players who know world capitals identify the city immediately; players who don't are left with nothing. Use `historic-capital` (which adds the character dimension) or `financial-hub` instead. |
| ~~`famous-landmark`~~ | Too specific, violates the conditional safety rule from the Clue Engine. A tag this specific makes a city identifiable to anyone who knows the landmark, bypassing deduction entirely. |
| ~~`population-large`~~ | Uninferential. Knowing a city is large doesn't help a player narrow the field in a meaningful way. Almost every city in the game is large. |
| ~~`safe`~~ / ~~`dangerous`~~ | Inappropriate and culturally loaded. These categories encode subjective and often racially charged perceptions of cities. Not an authoring vocabulary Global Pursuit will use under any circumstances. |
| ~~`developed`~~ / ~~`developing`~~ | Same problem as `safe`/`dangerous` — imprecise, loaded, and unhelpful as a deduction signal. Economic signals in the taxonomy (`financial-hub`, `industrial`, `agricultural`) capture the relevant economic character without the baggage. |
| ~~`multilingual`~~ | Requires knowing which cities are multilingual — geographic recall. Violates Pillar 1. |

---

## Interactions with Other Systems

| System | Direction | What flows |
|--------|-----------|------------|
| **Case Database** | Signal Taxonomy defines the valid vocabulary for | `signals[]` arrays on every WitnessClue; authors reference this document to populate those arrays |
| **Clue Engine** | Reads from Case Database | Receives WitnessClue objects including `signals[]`; passes them through to consuming UI systems without interpreting them |
| **City Selection UI** | Renders from WitnessClue | Renders `signals[]` as icons on the clue card alongside `clue_text`; icon assets are SVGs keyed by tag name |
| **Designer** | Produces assets defined by | One SVG icon per tag in this taxonomy (30 icons total); icon filenames must match tag names exactly for UI rendering |

## Dependencies

| System | Direction | Nature of Dependency |
|--------|-----------|----------------------|
| **Case Database** | Depends on Signal Taxonomy | The `signals[]` field on WitnessClue must only contain values from this taxonomy; the Case Database schema references this document as the authority |
| **Clue Engine** | Indirect dependency | Principle 5 (Text/Visual Split) in the Clue Engine directly references signal tags — the taxonomy must be stable before Clue Engine authoring rules are finalised |
| **City Selection UI** | Depends on Signal Taxonomy | Must implement an icon for every tag defined here; adding a tag to the taxonomy requires a corresponding icon deliverable before the tag can be used in authored cases |
| **Signal Taxonomy** | Depends on nothing | Foundational vocabulary layer — no upstream dependencies |

## Acceptance Criteria

**Taxonomy completeness:**
- [ ] Exactly 31 tags are defined across the 5 categories (6 Climate, 6 Terrain, 6 Economy, 8 Culture, 5 Infrastructure)
- [ ] Every tag has a description, example cities, a "when to use," and a "when not to use"
- [ ] No tag in this document is undefined in the Case Database schema validation rules
- [ ] Every tag has a corresponding SVG icon asset (Designer deliverable — tracked separately)

**Authoring validation (enforced at case review):**
- [ ] No WitnessClue has 0 signal tags
- [ ] No WitnessClue has more than 4 signal tags
- [ ] No WitnessClue uses a tag not present in this taxonomy
- [ ] No WitnessClue combines contradictory tags (`coastal` + `landlocked`, `polar` + `tropical`, etc.)
- [ ] Signal tags on each WitnessClue describe the target city, not the current city or any other city referenced in the clue text

**Rendering validation:**
- [ ] All tags in authored cases resolve to a valid SVG icon filename (no missing icon errors)
- [ ] Signal icons render on the clue card alongside witness text
- [ ] Signal icons do not render on city option pins on the globe

## Open Questions

| Question | Owner | Status | Resolution |
|----------|-------|--------|------------|
| Should the taxonomy include a `sub-saharan-african` culture tag to match the coverage level of the other regional culture tags? | Product Director | **CLOSED** | `sub-saharan-african` added to the Culture category 2026-03-26. Covers Lagos, Nairobi, Cape Town, Accra, and other sub-Saharan cities. Distinct from `middle-eastern` (North Africa). Icon deliverable required before any African city cases are authored. |
| Should `temperate` be removed as a standalone tag given the "avoid as standalone" guidance? | Product Director | **OPEN** | It exists primarily as a combination tag; if authors routinely misuse it standalone, replacing it with more specific tags may be cleaner. Revisit after the first authoring sprint. |
| Are 30 tags sufficient for the full planned city roster (200–500 cities)? | Product Director + Designer | **OPEN** | The taxonomy was designed for the MVP city set. Expansion may require additional tags — particularly for infrastructure (e.g. `metro-system`) and economy (e.g. `academic-hub`). Flag during Vertical Slice authoring sprint. |
| Should icon filenames be the tag name verbatim (e.g. `financial-hub.svg`) or use a prefix convention (e.g. `signal-financial-hub.svg`)? | Technical Director + Designer | **OPEN** | Naming convention must be agreed before asset delivery begins; UI rendering code must match the convention. |
