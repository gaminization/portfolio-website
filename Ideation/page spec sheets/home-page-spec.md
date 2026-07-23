# Home Page — Earth-Prime Universe — Complete Spec

Universe ID: `earth-prime` · Route: `/` · Reference: `universes/earth-prime/*`

Home is the entry point and the "control universe" — the one that establishes the shared visual grammar (mixed-media collage, handwritten annotation layer, purple/yellow/gold accent system) that every other universe will deliberately break from. Structurally, it's five fixed scroll bands (§3a in the architecture doc): content and band order never change between light and dark mode, only each band's visual treatment does.

The five bands have names, not numbers — used consistently in copy, code, and `narrative.ts`'s `content` field:

1. **Hero**
2. **Identity In Progress**
3. **Builder Manifesto**
4. **Achievement Constellation**
5. **Universe Gateway**

Each band is its own component under `components/home/bands/`: `HeroBand.tsx`, `IdentityBand.tsx`, `BuilderManifestoBand.tsx`, `ConstellationBand.tsx`, `UniverseGatewayBand.tsx` — `app/page.tsx` composes them in order per `narrative.ts`.

**A note on the wireframe/mask motif (write this into every asset brief, verbatim):** the "multiverse side" of the split portrait is an **abstract identity symbol** — not a superhero mask, not a specific character. Brief artists as "wireframe potential form / digital twin motif / becoming-process visualization," never as a costume or IP reference. This isn't just a style note — it keeps the project clear of any trademark/likeness issues given how directly Spider-Verse inspired the original concept.

---

## 1. Persistent / Global Elements (present across all five bands)

These live in the shared shell, not inside any one band — they scroll with the page but never disappear or reset.

| Element | Description | Behavior |
|---|---|---|
| **Top bar** | Transparent on load, gradually gains a blur/backdrop as the user scrolls past the Hero band | CSS `backdrop-filter`, scroll-linked opacity — cheap, no JS animation needed |
| **Top bar (left)** | `EARTH-PRIME` / `UNIVERSE 001` — small caps label, styled like a classified-document marker, with a small symbol beside it | Static; updates instantly if the user jumps universes mid-scroll via the Navigator |
| **Top bar (center)** | Primary nav: `01 HOME · 02 ABOUT · 03 PROJECTS · 04 BLOG · 05 RESUME · 06 CONTACT` | Current page underlined with a small purple dot beneath it; each item pre-warms the destination universe's assets on hover |
| **Top bar (right)** | `[ ALL REALITIES ]` button + `25% EXPLORED` + a small orbital graphic that continuously, slowly rotates | Opens `UniverseMap.tsx` on click — the expanded multiverse map view |
| **Custom cursor** | `DotCursor` skin — a small circle with a faint trailing delay | Cursor mutates into other skins only during page transitions (§7); stays a dot for the whole Home experience |
| **Multiverse Navigator** | Floating device, auto-minimizes during Identity In Progress and Achievement Constellation since those are the most immersive moments; reappears when idle or cursor nears screen edge | Per `useAutoMinimize.ts` |
| **Sound toggle** | Small waveform/mute icon, always visible | Controls Home's ambient layer independent of global mute — see §6 |
| **Mouse velocity readout** | A small live waveform reading raw cursor speed, bottom-left of Hero only | Cosmetic/atmospheric — visually foreshadows that velocity also drives the face-mesh glitch effect in Identity In Progress, so the visitor has already seen "your movement is being measured" before it pays off |

---

## 2. Band-by-Band Breakdown

### 1. Hero
**Background:** light/cream · **Scene mounted:** `FaceMeshScene` (idle/static state)

**Layout:**
- Massive display type, "GARV" and "ARORA" stacked, bleeding behind/around the central photo — the typography and the portrait interlock rather than sitting in separate zones.
- Center-right: a portrait of Garv, split vertically down the middle — left half a realistic photo, right half a purple/black wireframe "mask" render (the Spider-Verse-style dual identity motif, subject to the abstract-symbol note above). This is the *static* first frame of the face mesh that Identity In Progress will animate.
- Small "`001`" tag, top-left, underlined — a collectible/catalog numbering system that will recur (achievement stamps, project IDs elsewhere).
- Handwritten annotation, upper-right: *"not just building robots. building possibilities."* — the word "possibilities" highlighted in a marker-style swipe.
- Handwritten annotation, left of portrait: *"I don't just build things. I build worlds."* — "worlds" underlined.
- `IDENTITY: in progress` — a highlighted tag/pill, positioned lower-left, reinforcing the "who am I" theme that About Me will pick up.
- Right side, small caps: `// CURRENT STATUS` → `EXPLORING MULTIPLE REALITIES ...`
- Bottom-right: a taped-in polaroid of the Mars rover + a small ID-card-style tag reading `MISSION: BUILD / DISCOVER / REPEAT` — physical scrapbook artifacts, foreshadowing the Projects and Resume universes' physical-object language.
- Coordinates readout, far bottom-right: `36.7783° N / 119.4179° W` (placeholder/symbolic, not necessarily real location — matches Contact's "cosmic coordinates" idea).
- Small vertical color swatch stack (purple / magenta / gold / black) bottom-right — visually documents Earth-Prime's palette, almost like a designer's spec sheet baked into the page itself.
- Achievements list, left side, small caps with `+` bullets: `TEDx Speaker · Mars Rover Builder · School President · Researcher · Explorer · World Record Holder`.
- Bottom-center: `SCROLL TO EXPLORE` with a small animated mouse-scroll icon.

**Animation on entry:** type and portrait elements fade/settle in with a slight stagger (headline first, then portrait, then annotations/tags last) — handled by `lib/motion/stagger.ts`, not a single fade-in.

**Animation on scroll (within band):** subtle parallax — headline type drifts slower than the portrait, annotation tags drift at yet another rate, creating shallow depth without needing 3D for this band specifically. Mouse-velocity waveform updates live.

**Exit into Identity In Progress:** as the user scrolls past the fold, the portrait itself begins the transformation that the next band completes — this is a continuous scrub, not a hard cut. The wireframe half sheds a few loose particles that drift upward and off-frame as the headline fades — a small, cheap preview (a handful of `will-change: transform` DOM/canvas particles, not a full simulation) of the material-blend transformation the next band performs at full scale.

---

### 2. Identity In Progress
**Background:** dark/black (`inverted: true`) · **Scene mounted:** `FaceMeshScene` (active scroll-driven state)

This is the technical centerpiece of Home and the direct realization of your "digital twin" concept — and the single section that most needs to feel like **one continuous transformation** (Person → Data → Blueprint → Possibility), not four separate images swapping.

**Layout:** four large frames laid side by side, connected by simple arrows, forming a visual sequence rather than four separate images:

1. **Photo, profile turn** — the portrait rotates from front-facing to a 3/4 side profile.
2. **Point-cloud rendering** — the same head, now rendered as a sparse dot/point-cloud silhouette, still in profile.
3. **Wireframe grid** — the head fully turns back toward center, now rendered as a technical wireframe mesh with visible grid/construction lines (echoing the Resume universe's "Da Vinci reverse-engineering" blueprint idea).
4. **Particle dispersion** — the mesh breaks apart into a loose cloud of particles, implying the "digital twin" is unstable/still being built.

- Small caption top-left: `IDENTITY: IN PROGRESS` / `CONSTANTLY BUILDING. ALWAYS BECOMING.`
- Bottom-right caption: *"EVERY LINE OF CODE IS A PIECE OF ME."* — reveals character-by-character as the band settles into view, rather than fading in as a whole block.

**Scroll mechanics:** this entire band is driven by **one scroll-linked progress value** (0→1 across the band's height), which simultaneously controls three linked properties on a single R3F scene rather than four separately-triggered animations:
- **Rotation:** head turns front → profile → front again, mapped directly to scroll progress.
- **Material blend:** a shader uniform crossfades photo-texture → point-cloud → wireframe as progress increases (not a hard swap between three separate meshes).
- **Dispersion factor:** vertex offset magnitude (via noise) increases toward the end of the band, causing the mesh to visibly break into particles just before band 3 begins, then the particles reform/settle as band 3's content arrives.

**Reactive behavior (independent of scroll):**
- **Cursor velocity** feeds into the same dispersion shader — moving the mouse rapidly over this band causes the wireframe to glitch/scatter momentarily before reforming, layered on top of (not replacing) the scroll-driven state.
- Respects `prefers-reduced-motion`: if set, this band renders as a simple 4-frame static/crossfade sequence instead of a continuous shader-driven scrub — same content, no scroll-scrub or velocity-reactive glitching.

**Sound:** a low synthesizer hum (Tone.js-driven, not a static Howler loop) that subtly rises in pitch/intensity as scroll progress through this band increases, and glitches briefly in sync with any velocity-triggered dispersion.

**Continuity note (visual echo, not literal particle handoff):** to make the homepage feel like one evolving object rather than five independent sections, later bands reuse this band's particle **material/shader/color**, not the literal particle instances. Builder Manifesto's orbital dot, Achievement Constellation's stars, and Universe Gateway's card accents should all be rendered with the same point-sprite/glow shader established here. This achieves the "one continuous transformation" feeling at a fraction of the engineering cost of a single particle system persisting live across all five bands' scroll positions — which would require bands 3 and 5 (currently static/2D per the "90% artwork, minimal R3F" stack decision) to share a live WebGL context with this band, a real scope increase best avoided unless a prototype proves it's cheap enough.

---

### 3. Builder Manifesto
**Background:** light/cream · **Scene mounted:** `BuilderArmScene` (resolved decision — see below)

**Layout:**
- Left: four stacked declarative lines, large bold type, one per line — `A DREAMER.` `A BUILDER.` `A PROBLEM SOLVER.` `A RESEARCHER.` — the last word in the last line rendered in the accent purple and underlined, as a quiet visual "landing point" for the sequence.
- Far left, a thin orbital diagram: concentric elliptical rings with a single small purple dot orbiting, rendered using the same particle glow material as Identity In Progress (see continuity note above) — a minimal, almost scientific decorative motif.
- Right side: a short bio paragraph in a lighter weight — *"I merge creativity with technology to build machines that think, interfaces that inspire and systems that solve real problems. From robots to code, from ideas to impact — I'm here to make what matters, matter more. This is my journey across machines, stories and realities."* Reveals line-by-line as the band enters view, not as a single fade-in block.
- Far right: the robotic arm — see resolved behavior below.

**Animation:** the four declarative lines reveal with a staggered upward slide-in as the band enters view (`lib/motion/stagger.ts`), each roughly 100–150ms after the previous. The orbital diagram's dot continuously animates along its ellipse at a slow, ambient pace — always moving, not scroll-linked.

**Robotic arm — resolved decision: a real, lightweight R3F scene (`scenes/home/BuilderArmScene.tsx`), not a static parallax illustration.** Home is the flagship page, and giving the arm genuine (if limited) life makes the difference between "decorative image" and "the second of Home's three core symbols" (face = identity, arm = builder, constellation = journey). This is explicitly **not** the full IK rig that lives in the Projects room — no drag interaction, no inverse kinematics solving. Behavior:
- **Idle:** subtle, continuous servo micro-movements and occasional joint correction — small motor adjustments that make it read as "alive" without being distracting.
- **On scroll entry:** a brief power-up sequence plays once as the band comes into view — joints activate in sequence, an indicator light switches on, one deliberate movement settles it into its idle loop.
- **Cursor tracking:** the arm's head/end-effector subtly tracks cursor position within a small, tightly clamped range — a hint of attentiveness, never a full follow, and never so much motion that it reads as gimmicky.
- **Parallax:** in addition to its own idle motion, the whole arm sits on a slightly slower-moving parallax layer relative to the foreground text as the user scrolls through the band (`useArmParallax.ts` still applies, layered under the scene's own animation, not replacing it).
- **Rendering notes:** since Home already runs `FaceMeshScene` in the previous band, `BuilderArmScene` should mount/unmount based on viewport intersection (not run continuously off-screen) to avoid two live R3F canvases competing for GPU budget simultaneously. If both scenes ever need to render at once during fast scrolling/transition overlap, budget for it explicitly in perf testing rather than discovering it live.
- **Fallback:** if `FEATURES.enableFaceMesh`-equivalent flag for this scene is off, or on a detected low-end/slow-network device, degrade to the original parallax-illustration version (a static PNG/SVG with `translateY`/`translateX`/`rotate` only) — same visual position and composition, no live 3D.

---

### 4. Achievement Constellation
**Background:** dark/black (`inverted: true`) · **Scene mounted:** constellation interaction layer (2D canvas or lightweight R3F points, sharing the particle material established in Identity In Progress)

This replaces a conventional timeline entirely, per your original brief — achievements are discoveries, not a resume list.

**Layout:**
- Header, top-left: `MOMENTS THAT SHAPED THE BUILDER` / *"Stars in the journey, not just milestones."*
- A field of stars connected by thin dotted lines forming a loose constellation shape across the band's width, each star labeled with a milestone and year — these are the **primary, always-visible stars**:
  - `WORLD RECORD — TAEKWONDO — 2008`
  - `EXCHANGE PROGRAMS — DUBAI | MAURITIUS — 2023`
  - `TEDx SPEAKER — STANDING UP AGAINST GENDER STEREOTYPES — 2023`
  - `SCHOOL PRESIDENT & HEAD BOY — 2022`
  - `MARS ROVER BUILDER — 2024`
  - `RESEARCHER — ALWAYS LEARNING, ALWAYS EXPLORING`
- **Secondary/hidden stars** — smaller, dimmer, positioned in the gaps between primary stars, only becoming clearly visible on close attention or hover-proximity (not permanently as bright as the primary six). These are the easter-egg layer: small personal moments rather than headline achievements — e.g. *first robot built*, *first line of code*, *a formative failure*, *a teacher's influence*, *a favorite book*, *a meaningful memory*. **Content gap to close before build:** these six placeholder categories need real specifics from you before this can ship — right now they're a structural idea, not written content.
- Handwritten prompt, bottom-right, with a small pointing arrow: *"hover a star to know more."*
- The whole constellation drifts with a very slow, almost imperceptible ambient motion (stars gently shift position over tens of seconds) — not scroll-linked, just a sign the "map" is alive rather than a frozen diagram.

**Interaction:**
- On hover/tap, a star brightens, its connecting lines pulse subtly, and its full label/description appears.
- Clicking a primary star triggers a brief in-place zoom/flash (reusing the `portal` effect from the shared transition effects library at reduced scale/duration) that expands that star's detail card in place — recommended for v1 over deep-linking to Blog/Resume, since that content isn't built yet. Revisit as a v2 enhancement once there's real Blog/Resume content to link to.
- Clicking a secondary/hidden star opens a small, simpler card — just the short personal note, no "impact" framing, since these are meant to feel like discoveries, not credentials.
- **Accessibility:** a parallel, always-visible **List View** toggle (same pattern as the Projects room's accessible list) renders all primary milestones (and, ideally, the secondary ones too, clearly marked as "hidden extras") as a plain, fully-readable list for screen readers or reduced-motion users — the constellation is a presentation layer over real semantic content, never the only way to access it.

**Note on achievement↔universe relationships:** rather than trying to visually connect this band to Universe Gateway (two sections that are never on-screen together, making a live cross-band hover-highlight interaction largely invisible to the user triggering it), the relationship is expressed as **data**, surfaced later — see Universe Gateway below.

---

### 5. Universe Gateway
**Background:** light/cream · **Scene mounted:** none (small per-card Lottie/Rive micro-loops only — see below)

**Layout:**
- Left: heading `EVERY REALITY HAS A PURPOSE`, subtext *"Different worlds. Different stories. One mission. Where will you go?"*, a small wireframe globe icon.
- Right: five vertical universe cards in a row, each a distinct illustrated/rendered scene previewing that universe's visual identity, with a title and one-word subtitle:
  - **ABOUT** — "The Origin" (cool blue/aquarium tone)
  - **PROJECTS** — "The Workshop" (warm orange)
  - **BLOG** — "The Journal" (purple/blue tech-bust tone)
  - **RESUME** — "The Archive" (green marble-bust tone)
  - **CONTACT** — "The Signal" (magenta/purple astronaut tone)
  - Each card has a small `+` icon, bottom-right corner, signaling "expand/enter."

**Live micro-previews (adopted — cheap, high payoff):** rather than a static teaser image, each card runs a small looping animation via Lottie or Rive (lightweight, not R3F — keeps this band's "mostly artwork" budget intact):
  - **About:** a tiny Aero-OS desktop — a folder opens, a bubble drifts, a music player pulses.
  - **Projects:** the workshop's robot arm gives a small servo twitch; the rover's headlights blink.
  - **Blog:** ASCII particles briefly assemble into a glyph; a terminal cursor blinks.
  - **Resume:** a wax stamp presses down; a blueprint line draws itself.
  - **Contact:** the astronaut's hand gives a small pulse; signal waves ripple outward.

**Related-achievement badges (resolves the achievement↔universe connection):** each card carries 1-3 small badge icons in a corner, tagging which Achievement Constellation milestones relate to that universe — e.g. the Projects card shows small icons for "Mars Rover Builder" and "Researcher"; the Blog card shows "TEDx Speaker." This is a data relationship (each achievement tagged with a related `UniverseId` in `content/achievements.json`), surfaced here rather than as a live cross-scroll hover-highlight back in the Constellation band, since the two sections are never on-screen simultaneously.

**Interaction:** hovering a card triggers a subtle zoom/parallax on that card's artwork (not the others) and swaps the custom cursor to a small preview of *that universe's* cursor skin.

**On click — selection exit animation (adopted):** rather than cutting straight to the shared transition sequence, Universe Gateway plays its own brief pre-transition first:
1. All **non-selected** cards fade, blur slightly, and shrink toward their center.
2. The **selected** card expands, lifts (subtle scale + shadow increase), and begins consuming more of the viewport.
3. Only once that settles does the standard transition sequence begin (composing the exiting-Home effects from `universes/earth-prime/transitions.ts` with the destination universe's entering effects).

This makes choosing a universe feel like a deliberate commitment rather than a plain link click — worth implementing as a small dedicated state (`selecting`) inserted just before `exiting` in the transition machine, scoped specifically to this band's card-click trigger rather than a change to the general-purpose state machine used everywhere else.

**Animation on entry:** cards fade/slide in with a staggered reveal as the band enters view. No scroll-linked scrubbing needed here — this band is the deliberately calmer "landing pad" after Achievement Constellation.

---

## 3. Persistent Bottom Navigation

A secondary, fixed bottom bar (dark, minimal) mirrors the six-page nav using icon + label pairs (asterisk/Home, person/About, cube/Projects, leaf/Blog, document/Resume, radio/Contact), with the active page's icon in the accent purple. This gives a reachable, thumb-friendly nav on both desktop (as a secondary/persistent affordance) and mobile (where it likely becomes the *primary* nav, consistent with the bottom-tab-bar pattern already established for mobile in other universes).

---

## 4. Performance Budget (hard rule, not a guideline)

All Home animations — across every band — must be built from:
- **Allowed:** `transform`, `opacity`, `filter`, shader uniforms, canvas/WebGL transforms.
- **Avoid during animation:** `width`, `height`, `top`, `left`, `margin`, `padding` — anything that triggers layout recalculation.

Targets: **60fps minimum on desktop** (90–120fps on high-end hardware is the aspirational ceiling, not the floor), **stable 60fps on mobile**, **30fps acceptable as a hard floor on confirmed low-end devices** (gated via `lib/network-tier.ts`/device detection, not assumed). This should be enforced in Lighthouse CI and spot-checked manually on Home specifically, since it carries the heaviest animation load of any universe.

---

## 5. Dark Mode Variant — "NEXUS"

Per your confirmation, dark mode uses the **same five bands, same content, same order** — only the design of each band changes. Confirmed differences for the hero band specifically (from the mobile NEXUS mockup, the fullest reference available for dark mode so far):

- Background shifts from cream to full black.
- The split photo/wireframe portrait is replaced by a more scattered, ink-and-static aesthetic — cracked/splattered black-and-white texture over the photo half, with a glowing purple mask-eye motif standing in for the wireframe half (a more literal "mask" than the grid-wireframe look of light mode).
- A `NEXUS` wordmark/insignia (a stylized mask icon) appears — this is Earth-Prime's dark-mode alter-ego branding, with its own tagline: *"Not a hero. Just a reflection of what I'm becoming."*
- Handwritten annotations remain but shift to a looser, more scrawled/urgent style ("Every pixel a possibility. Every movement a choice.") rather than the calmer marker-highlight style of light mode.
- Achievements list and mouse-velocity waveform persist in the same positions — confirming these are shell-level elements, not band-specific ones, and don't need to be redesigned per mode.

Identity In Progress, Builder Manifesto, Achievement Constellation, and Universe Gateway's dark-mode-specific redesigns aren't yet mocked in as much detail as Hero — flag this as the next reference gap to close before implementation reaches those bands, rather than guessing at their NEXUS treatment now.

---

## 6. Sound Design Summary

| Band | Ambient layer | Notable one-off sounds |
|---|---|---|
| Hero | Low, calm ambient pad | Soft "paper/tape" rustle on annotation reveal |
| Identity In Progress | Scroll/velocity-reactive synth hum (Tone.js) | Brief glitch/static burst on rapid cursor movement |
| Builder Manifesto | Ambient continues, slightly warmer | Faint servo tick synced to the arm's idle micro-movements |
| Achievement Constellation | Ambient quiets further, near-silent | Soft chime per star on hover; slightly louder tone on click; a subtler, higher chime for secondary/hidden stars |
| Universe Gateway | Ambient fades toward the destination universe's own ambient track | A short "whoosh" per card hover, foreshadowing that universe's transition sound; a brief rising tone on the selection exit animation |

All governed by `audioStore`'s category volumes (music/ambience/effects), independent of the global mute.

---

## 7. Performance & Accessibility Notes Specific to Home

- **`prefers-reduced-motion`:** disables the scroll-scrub on Identity In Progress (falls back to a static crossfade sequence), disables cursor-velocity glitching entirely, and reduces Hero/Builder Manifesto/Universe Gateway's parallax to a simple fade-in. Achievement Constellation keeps hover-to-reveal but drops any pulse/glow animation on the connecting lines and the ambient constellation drift.
- **Face mesh and arm scene assets:** both gated behind feature flags (`FEATURES.enableFaceMesh` and an equivalent for `BuilderArmScene`) — if disabled (very low-end devices, or before the asset pipeline is ready), Identity In Progress degrades to a simpler animated crossfade of the same four states, and Builder Manifesto degrades to the static parallax-illustration fallback described in §2.3, rather than either band going blank.
- **Network-aware loading:** on a detected slow connection, Home should load Hero immediately but lazy-load the face-mesh `.glb` and the arm scene's assets, and defer Builder Manifesto/Achievement Constellation/Universe Gateway's illustration and Lottie/Rive assets until they're near-viewport, per `lib/network-tier.ts`.
- **Screen reader path:** every band's core message should be reachable as plain readable text without relying on the visual/animated presentation — Achievement Constellation's List View toggle (§2.4) is the clearest example, but Hero, Builder Manifesto, and Universe Gateway's headline copy should also be real semantic headings, not text-in-image.
