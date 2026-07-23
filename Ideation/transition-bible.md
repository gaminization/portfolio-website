# The Transition Bible — Complete Cross-Universe Transition Specification

Reference: `components/transitions/*`, `lib/transitions/machine.ts`, each universe's `universes/{id}/transitions.ts`

This document is the missing piece that ties Home, About Me, Projects, Blog, Resume, and Contact together into one actual multiverse rather than six well-designed but separate pages. If the six universe specs describe *where you are*, this one describes *the moment you're between two realities* — arguably the single most important experiential detail in the entire site, since it's the one thing every visitor will feel dozens of times, regardless of which universe they linger in longest.

---

## 0. Why This Matters As Much As Any Single Universe

Your original inspiration was explicit: this should feel like *Spider-Verse*. It's worth being precise about what that actually means for engineering and design, not just as a mood reference:

- **Each universe has its own visual language, the same way each Spider-Person's home dimension has its own animation style** — hand-painted, brutalist, 8-bit, noir. Crossing between them isn't supposed to feel seamless; the seam is the point.
- **Reality visibly strains and tears when a crossing happens** — Miles's literal glitching between dimensions, the "instability" of not belonging fully to one reality yet, is the emotional core of the films' visual language. Every transition on this site should carry a bit of that same instability — a glitch, a collapse, a decay — never a plain cross-fade pretending nothing structurally different is happening.
- **The multiverse is vast and it knows it** — the Navigator's `[ALL REALITIES]` / `% EXPLORED` framing exists because Spider-Verse's multiverse is enormous and mostly unseen; six universes should feel like a visited fraction of something bigger, not the entire world.

Concretely, this means: **transitions are not decoration bolted onto navigation — they're where the "multiverse" idea actually lives or dies.** A visitor could read every word on every page and still not feel the multiverse concept if the transitions between them are generic. This is the highest-leverage remaining piece of the whole design.

---

## 1. System Architecture (recap, self-contained)

Per the locked architecture: a transition is not six bespoke page-pair animations, but a **state machine composing small, reusable atomic effects**, each declared once per universe:

```
idle → exiting → in-transit (stage 1..4) → entering → arrived
```

Each universe's `universes/{id}/transitions.ts` declares two arrays:

```ts
export const transitions = {
  exiting:  ['glitch', 'collapse'],        // played when LEAVING this universe
  entering: ['portal', 'archiveRebuild'],  // played when ARRIVING at this universe
};
```

**The combinatorial payoff:** because every universe only ever needs to define its *own* exit and entry once, the transition between *any* two universes — not just adjacent ones in the "main loop" — is automatically the composition of [origin's exiting effects] + [destination's entering effects]. Six universes × (exiting + entering) = 12 declarations produce all 30 possible directed transitions, without hand-authoring 30 bespoke animations. This is the same "one system, many combinations" principle used for the Innovation Graph, the theme resolver, and the Reference Block elsewhere in this project, applied to motion instead of data.

---

## 2. The Canonical Effects Library

Twelve atomic, reusable effects — each a small, independent animation component (`components/transitions/effects/*.ts`), composed two or three at a time per universe:

| Effect | What it looks like |
|---|---|
| `glitch` | RGB channel split, brief static/interference, screen-tear flicker |
| `collapse` | Everything pulled inward toward a vanishing point — a vortex/black-hole effect |
| `portal` | A swirling doorway or wormhole opens, framing the destination beyond it |
| `desktopCrash` | Fake OS error dialogs multiply and stack, then the "window" visually shatters into fragments |
| `archiveRebuild` | Sketch/ink linework draws itself into being — the destination scene assembling stroke by stroke |
| `flatten` | The current view compresses, losing a dimension, as if pressed flat |
| `pixelate` | The flattened view breaks into a coarse dot-matrix/pixel pattern |
| `asciiForm` | Pixels resolve into readable monospace ASCII characters |
| `decay` | Text and imagery dissolve into scrambled character-soup/static |
| `paperUnfold` | A folded document/case visibly opens and smooths flat into the page layout |
| `paperBurn` | Edges visibly catch fire and burn away, revealing what's beneath |
| `signalWarp` | A rising synth-driven glitch that resolves into a clean signal — interference clearing into clarity |

Every universe's `entering`/`exiting` arrays are built entirely from this list — no universe invents a one-off effect exclusively for itself, which keeps the total system small even as it produces a fully connected 6×6 transition matrix.

---

## 2a. The Multiverse Corridor (the space between universes)

The `in-transit` state currently exists as a machine concept but didn't yet have its own visual identity — worth naming and building deliberately, since Spider-Verse always gives dimensional travel a recognizable "place" (the Collider tunnel, the Go Home Machine), not just a fade between two scenes.

**The Corridor is a shared overlay, not a seventh universe** — a brief, universe-agnostic transit space every crossing passes through, populated by small fragments from the six universes: face-mesh particles, drifting ASCII glyphs, scattered OS-window fragments, ink-sketch linework scraps, paper ash, signal-constellation stars. Critically, **almost none of this is new asset work** — every one of these fragment types is something each universe's own spec already establishes for other reasons (Home's Identity In Progress particles, Blog's ASCII motif, About Me's desktop-crash fragments, Projects' linework style, Resume's paperBurn ash, Contact's constellation stars). The Corridor mostly just reuses what already exists, briefly, in one more place.

**Universe signatures/residue — the mechanism that populates it:** rather than a universe's fragments vanishing the instant its `exiting` effect completes, a small trail of that universe's specific residue continues to drift briefly into the Corridor itself — face vertices lingering after leaving Home, ASCII characters continuing to drift after leaving Blog, ash remaining after leaving Resume, signal particles persisting after leaving Contact. This is what actually makes the Corridor feel populated by *this specific* journey rather than a generic transit animation reused identically for every jump.

**Transition context — already implicit in the locked state machine, just worth stating explicitly:** the `in-transit` state already carries `{ from, to }` (per §4 of the architecture doc's transition machine type). The Corridor reads directly from this — a Resume→Blog crossing shows paper-ash and ASCII fragments together; a Home→Contact crossing shows face-mesh particles and signal stars together. This is cheap specifically because the data was already there; the Corridor is a new *renderer* of existing state, not a new data requirement.

---

## 3. What Changes During Every Transition (the full checklist)

Per your own transition storyboard notes — `SOUND WARP · COLOR SHIFT · CURSOR MUTATES · TYPOGRAPHY CHANGES` — every crossing, without exception, touches all of these simultaneously, not just the visual effect:

1. **Visual effect** — the composed atomic effects from §2.
2. **Cursor mutation** — the cursor skin swaps mid-transition (§6), not abruptly at arrival.
3. **Sound crossfade** — the ambient track fades from the origin universe's mix into the destination's (§7), timed to the visual effect, not a hard cut.
4. **Color palette shift** — the resolved theme tokens (`resolveTheme`) for the destination universe apply progressively as the transition resolves, so the color shift *is* part of the animation, not something that just appears after.
5. **Typography swap** — the destination's font pairing (display/body/mono) loads in as content settles, per universe theme tokens.
6. **Navigation chrome restyle** — the shared nav bar's visual treatment (§2 of each universe spec) updates to match the destination universe as it resolves.

A transition that only changes the visual effect but not the other five is an incomplete transition — worth treating this checklist as a literal QA list per universe pairing, not just a description.

---

## 4. The Six Primary Loop Transitions

These are the confirmed, storyboarded sequences — the "main path" a visitor follows using the primary nav in order. Each other possible pairing (§5) is generated automatically from the same underlying per-universe declarations, but these six deserve to be described in full since they're the ones with real reference material behind them.

### 4.1 Home → About Me

- **Exit (Earth-Prime):** `glitch` (reality visibly breaks — chromatic aberration, brief tear) → `collapse` (the scene pulls inward into a vortex).
- **Entry (Aero-OS):** the universe's own documented boot sequence (About Me spec §1a) — `AERO OS / Version 3.14`, a loading bar (`Loading Memories... Loading Curiosity... Loading Ambition... Loading Dreams...`), a startup chime, ~1.5s.
- **Cursor:** `DotCursor` → `BubbleCursor`.
- **Sound:** a warping glitch-shift tone, resolving into Aero-OS's warm ambient pad.
- **Feeling:** stepping through a tear in reality into a gentler, more nostalgic dimension.

### 4.2 About Me → Projects

- **Exit (Aero-OS):** `desktopCrash` — fake OS error windows multiply and stack (a small in-joke — the desktop literally "crashes" on the way out), then break apart into scattered fragments.
- **Entry (Cuphead-verse):** `archiveRebuild` — the workshop scene draws itself into being with visible sketch linework, consistent with Projects' own "stronger linework, hand-crafted" art direction (Projects spec §1) — the transition's drawing style previews the destination's actual visual language.
- **Cursor:** `BubbleCursor` → `HandCursor`.
- **Sound:** a glitch burst into soft ink/paper rustle.
- **Feeling:** the desktop breaking down becomes the raw material the workshop is sketched from.

### 4.3 Projects → Blog

- **Exit (Cuphead-verse):** `flatten` → `pixelate` — the illustrated room compresses and coarsens.
- **Entry (Brutalist Archive):** `asciiForm` — the pixelated view resolves into monospace ASCII. **This is the same visual language Blog uses internally when opening a single post** (Blog spec §5) — arriving at the archive from anywhere and opening one specific entry inside it both resolve through the identical flatten→pixelate→ASCII-form pattern, reinforcing that this universe's whole identity runs on one consistent decode language, not two unrelated effects that happen to share a name.
- **Cursor:** `HandCursor` → `PixelCursor`.
- **Sound:** a fade into a soft typewriter-key rhythm.
- **Feeling:** the workshop's warmth gives way to something colder and more textual.

### 4.4 Blog → Resume

- **Exit (Brutalist Archive):** `decay` — text and imagery dissolve into scrambled character-soup, then fall apart entirely.
- **Entry (Dossier):** the universe's own documented unfolding sequence (Resume spec §3) — `portal` opens → a case/document container appears (with the small verification flourish) → the wax seal visibly breaks → the document unfolds into the page layout.
- **Cursor:** `PixelCursor` → `QuillCursor`.
- **Sound:** static resolving into the sound of a page turning.
- **Feeling:** the archive's scrambled data resolves into something physical and permanent — data becoming paper.

### 4.5 Resume → Contact

- **Exit (Dossier):** `paperBurn` — the document's edges visibly catch fire and burn away.
- **Entry (Vapor-Signal):** the universe's own documented connection sequence (Contact spec §2) — `Establishing Connection` (brief static/glitch) → `Signal Locked` (the vaporwave scene resolves) → `Channel Open` (the terminal and hand settle into place).
- **Cursor:** `QuillCursor` → `GlitchCursor`.
- **Sound:** a rising tone into spacious synth-pad ambience.
- **Feeling:** the burned dossier's ashes become signal static, then resolve into a clear transmission.

### 4.6 Contact → Home (closing the loop)

- **Exit (Vapor-Signal):** `signalWarp` reversed — the clean signal breaks back down into interference, collapsing inward.
- **Entry (Earth-Prime):** `portal` opens into **Identity Reconstruction** — Home's own named arrival ritual, closing a real gap where every other universe had one and Home didn't. Four stages, mirroring Home's own Identity In Progress motif in reverse: **(1) Particles** — scattered points with no discernible shape yet; **(2) Wireframe** — the points connect into a recognizable but unfinished mesh; **(3) Half-real portrait** — the photo-texture half resolves while the wireframe half remains; **(4) Complete face** — the sharp photo/wireframe split hero image fully resolves. This plays identically **regardless of which universe the visitor is arriving from**, the same way About Me's OS Boot plays whether you arrived from Home, Projects, or a direct link — not a bespoke flourish only used for this one specific pairing.
- **Cursor:** `GlitchCursor` → `DotCursor`.
- **Sound:** signal static resolving into Home's calm ambient pad.
- **Feeling:** the loop closes — the multiverse's edge (Contact) leads back to its center (Home), rather than the journey feeling like it dead-ends.

**All six named arrival rituals, for reference — Home now has one, matching parity with the other five:**

| Universe | Named Entry Ritual |
|---|---|
| Earth-Prime (Home) | Identity Reconstruction |
| Aero-OS (About Me) | OS Boot |
| Cuphead-verse (Projects) | Workshop Assembly (`archiveRebuild`) |
| Brutalist Archive (Blog) | ASCII Decode |
| Dossier (Resume) | Dossier Unfold |
| Vapor-Signal (Contact) | Signal Lock |

---

## 5. The Other 24 Transitions (the combinatorial rule)

A visitor can jump to any universe from any other via the primary nav or the Multiverse Navigator's map — not just follow the loop in order. **These are not hand-authored** — each is automatically [origin's `exiting` effects] + [destination's `entering` effects], per §1's architecture. A few worked examples to confirm the rule produces sensible results, not just technically-valid ones:

- **Home → Blog directly** (skipping About Me and Projects): `glitch` + `collapse` (Home's exit) into `asciiForm` (Blog's entry) — reality breaking apart, then resolving into archive text. Reads coherently even without the intermediate universes.
- **Resume → Projects directly:** `paperBurn` (Resume's exit) into `archiveRebuild` (Projects' entry) — burning paper giving way to a hand-sketched workshop forming. Also coherent — arguably a nice unplanned pairing (paper becoming a sketch).
- **Contact → About Me directly:** `signalWarp` reversed (Contact's exit) into the Aero-OS boot sequence (About Me's entry) — signal breaking down into a fresh OS boot. Works without needing a bespoke pairing.

**A real QA task, not an assumption:** because these 24 pairings are mechanically generated, they should be spot-checked once built, not assumed to all look equally polished purely because the system technically produces them. A composition that's *technically correct* isn't automatically as considered as the six hand-referenced primary pairings above — budget a review pass across the full matrix before calling this done, rather than treating "the architecture supports it" as equivalent to "it looks good."

### 5a. Cross-Universe Instability Event (V2, very rare)

A small, deliberately rare enrichment to the Corridor (§2a): roughly once in every 75 transitions, a single fragment from a universe that's neither the origin nor the destination briefly drifts through — an ASCII glyph during a Projects→Resume crossing, a paper scrap during an About Me→Contact crossing, a constellation star during a Blog→Home crossing. Not random noise generated every time — genuinely rare, and meaningful specifically because of that rarity: it reads as evidence the universes bleed into one another at the edges, a core Spider-Verse idea, rather than a decorative flourish diluted by overuse. Keep the pool of possible "bleed" fragments small and deliberate, consistent with the "a few well-placed surprises beat many thin ones" discipline already applied to every universe's own easter eggs.

---

## 6. Cursor Evolution Across the Journey

The six cursor skins, matching the primary loop order:

`DotCursor` (Earth-Prime) → `BubbleCursor` (Aero-OS) → `HandCursor` (Cuphead-verse) → `PixelCursor` (Brutalist Archive) → `QuillCursor` (Dossier) → `GlitchCursor` (Vapor-Signal) → back to `DotCursor`.

The cursor doesn't swap abruptly at the moment of arrival — it mutates *during* the in-transit stage, so by the time the destination universe is fully visible, the new cursor is already active and has had a chance to visually transform rather than just appear.

---

## 7. Sound Crossfade

Per the locked `AudioManager` (a singleton, not per-universe instances): the ambient track crossfades from origin to destination over the course of the transition, timed to the visual effect stages rather than cutting hard at any single moment. Each universe's specific ambient character is already documented in its own spec (§9-ish "Sound Design" section in each) — the Transition Bible's job is just the *crossfade*, not re-describing each destination's steady-state ambience.

**The Multiverse Transit Tone (adopted):** beyond the crossfade between two universes' distinct ambiences, every single transition — all 30 possible pairings, without exception — plays one shared, short audio motif (roughly 300–500ms), layered on top of the crossfade rather than replacing it. This is the sonic equivalent of Spider-Verse's dimensional-travel sound cue: no matter which two realities are involved, crossing between *any* of them shares one recognizable signature, reinforcing that a "jump" is happening at all, distinct from the two universes' own individual identities. Cheap to build (one short sound asset, triggered identically every time) and disproportionately effective at making transitions feel like one coherent system rather than six independently-designed page changes.

---

## 7a. Navigation as Universe Selection (a hover preview)

The biggest remaining opportunity to make ordinary navigation itself feel like part of the multiverse concept, not just a means to trigger one: **hovering a nav item briefly previews that destination's signature material**, without triggering an actual transition — a flash of ASCII texture on hovering "Blog," a hint of paper fiber on hovering "Resume," a scatter of signal stars on hovering "Contact." Kept extremely brief (50–100ms), just enough that clicking the nav starts to feel like *choosing a reality* rather than following a link.

**This is cheap specifically because of the Corridor's asset reuse (§2a):** the same small fragment/signature assets already built for the Corridor and for Cross-Universe Instability (§5a) are exactly what a hover preview needs — this section mostly asks "use those same assets in one more place," not "build a new asset category."

- **V1 (simple):** a brief color/tint flash on the nav item matching the destination universe's accent palette — cheap, immediate, requires no new assets beyond the theme tokens that already exist.
- **V2 (full fidelity):** the actual signature-fragment preview described above (ASCII flash, paper fiber, signal stars, etc.) — reuses Corridor assets directly, but is real animation work per nav item, worth sequencing after the Corridor itself is built and proven.

---

## 8. Direct URL Loads & Bookmarking

Per the locked architecture: a visitor landing directly on `/resume` (a shared link, a bookmark, a fresh tab) should **not** sit through the full multi-stage choreography of an unrelated "exiting Home" sequence they never actually experienced. Applied concretely per universe:

- **About Me direct load:** the AERO OS boot sequence plays once, briefly (its own natural "entry," not compressed further — it's already short).
- **Resume direct load:** compresses to just the wax seal snapping once, document already resolved — skipping the full portal/case/unfold staging.
- **Contact direct load:** compresses to a simple fade directly into the resolved scene, skipping the Establishing Connection/Signal Locked staging.
- **Home, Projects, Blog direct loads:** a simple, fast fade into the resolved page — no borrowed "exiting" flourish from a universe that was never actually left.

The general rule: **every universe's own "entering" sequence already has a documented simplified/`prefers-reduced-motion` version (per that universe's own spec) — direct loads use that same simplified version**, rather than needing a third, separately-authored variant. Two states per universe (full choreography, simplified), not three.

---

## 9. Accessibility

Consolidated from each universe's own spec, restated here as the cross-cutting rule: **`prefers-reduced-motion` collapses every transition, in both directions, to a simple cross-fade** — no glitch, no collapse, no multi-stage staging, in either the exiting or entering half. This is a single global check in `TransitionManager`, not six separate implementations of the same rule. The destination universe's actual content is unaffected either way — only the presentation layer of *arriving* at it changes.

---

## 10. Performance Notes

- All twelve effects (§2) are DOM/CSS/canvas-based, consistent with the site-wide principle that only Home and Projects use real R3F — a transition should never require spinning up a WebGL context just to move between two pages, even when the destination itself uses R3F (Home's face mesh, Projects' room). The transition plays *before* the heavier scene mounts, not concurrently with it.
- Because every visitor experiences transitions far more often than any single universe's deeper interactions, these twelve effects deserve disproportionate performance attention relative to their apparent simplicity — a laggy transition undermines every universe it connects, not just one.
- Preload the destination universe's critical assets (per `lib/network-tier.ts` and the asset manifest) during the `exiting`/`in-transit` stages, so `entering` doesn't stall waiting on a network request that could have started several hundred milliseconds earlier.

---

## 11. V1 / V2 Scope

Given how central this system is to the entire multiverse concept, less gets deferred here than in most individual universe specs — and most of this round's additions are cheap specifically because they reuse assets each universe already needs for other reasons, not new asset categories.

**V1 — Launch Critical**
- All twelve atomic effects (§2), the Multiverse Corridor and universe-signature residue (§2a — cheap, mostly reused assets, and foundational to the whole "this is dimensional travel" feeling), all six primary loop transitions in full (§4, including Home's now-named Identity Reconstruction ritual), the combinatorial rule producing the other 24 (§5, even if not every pairing has been individually polish-reviewed yet), cursor mutation (§6), sound crossfade plus the shared Multiverse Transit Tone (§7 — cheap, one asset, disproportionately effective), a simple color-flash version of nav hover preview (§7a, V1 tier), direct-load compression (§8), and the reduced-motion global rule (§9).

**V2 — Recommended, Not Required for Launch**
- The full spot-check/polish pass across all 24 non-primary pairings (§5) — the mechanically-generated versions are real and functional in V1; individually reviewing and hand-tuning each one for the same level of considered pairing as the six primary transitions is worth doing, just not a launch blocker.
- **Cross-Universe Instability Events** (§5a) — the rare 1-in-75 fragment bleed-through; genuinely delightful, genuinely optional.
- **Full-fidelity nav hover preview** (§7a) — the actual signature-fragment animation, versus V1's simpler color-flash version.
