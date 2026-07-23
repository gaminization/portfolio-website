# Resume Page — Dossier Universe — Complete Spec

Universe ID: `dossier` · Route: `/resume` · Reference: `universes/dossier/*`

The emotional pitch, direct from your original brief: **this is not a resume, it's a recovered document** — an artifact pulled from another reality, physically aged, annotated, sealed, and reopened for inspection. Where Vulture's universe in *Across the Spider-Verse* gave you the visual reference (sepia, technical, faintly menacing precision), the emotional target here is warmer: *this document was made by someone who builds things and wants you to see the proof.*

**One real constraint that doesn't apply to any other universe:** this page has to actually function as a resume. A recruiter or hiring manager needs to read it quickly, and many will want a normal, clean, printable copy — the creative experience is a layer on top of a genuinely usable document, never a replacement for one. Keep this in mind throughout; it's the "what not to build" guardrail for this universe (§14).

**A tone decision worth being explicit about:** a later proposal reframed this universe as a military/classified-intelligence dossier — Lockheed engineering manuals, "CLEARANCE LEVEL" structures, spacecraft technical documentation. That's a genuinely different emotional register than what's actually established — your original brief's Vulture's-Universe reference and the approved mockups (wax seals, handwritten field notes, "Curiosity fuels exploration. Discipline builds impact.") are warm and personal, layered with technical precision, not cold and procedural. Keeping the confirmed warmer direction; a few specific pieces of the militaristic proposal are still worth borrowing on their own merits (classification tags on achievements, §8; a small verification flourish within the existing entry sequence, §3) without adopting its overall tone.

---

## 0. V1 / V2 Prioritization

**V1 — Launch Critical**
- The physical paper system (§4), the six document-sections (§6), the Technical Blueprint Portrait as hero (§5, now scaled as the page's centerpiece), the wax seal and Dossier Metadata box, the Achievement Stamps with tiering (§8), the Current Status sheet (§5a), a stronger End of Dossier closing (§3a), basic scroll-reveal animation, dark mode (§12, already confirmed by mockup), mobile accordion (§13), and PDF export (§11a — this is a hard requirement, not flavor).
- The unfolding entry sequence (§3) in a simplified form — portal/collapse handoff from the previous universe, wax seal breaks, document settles into view.

**V2 — Recommended, Not Required for Launch**
- Full **Dynamic Ink** scroll-drawing on every diagram (§9) — ship with it on the portrait and one or two hero diagrams first; extending it to every sketch on the page is a nice-to-have, not a blocker.
- The UV-light cursor discovery mechanic (§10) — genuinely delightful, but a discrete enhancement layered onto content that already stands on its own without it.
- Engineering Sketch Overlays (§7) beyond one or two hero placements — start with the portrait and the Mars rover sketch; wallpapering every section with faint background diagrams is a polish pass, not core.
- **Recruiter Mode** (§14, new) — a real, valuable addition, but PDF export already covers the hard "must actually work as a resume" requirement for V1; this is an enhancement for in-page browsing, not a launch blocker.

---

## 1. Visual & Style Direction

Primary register: **an aged physical document, not a webpage wearing a paper texture.** Sepia/brown base palette (dark mode: charred black paper with amber ink — already confirmed via existing mockups, not a proposal), technical-drawing linework throughout, and a deliberately "monotonous," restrained color story — this isn't a colorful scrapbook, it's closer to a technical dossier that happens to have some warmth and wear to it.

Physical details established by your original brief, all required, not optional flourishes: curled corners, folded sections, torn edges, visible paper depth/shadowing where sheets overlap, small coffee stains, tape holding things together, faint paint spots in the background. **This stays outside the R3F budget**, consistent with Blog and Contact — the paper-physicality effects are CSS (box-shadow, clip-path for torn edges, layered background textures, SVG filters for paper grain) over a standard DOM layout, not a 3D scene. Only Home and Projects use real R3F.

---

## 2. Persistent / Global Elements

| Element | Description | Behavior |
|---|---|---|
| **Top bar** | A compass icon, standard six-item nav re-skinned in the dossier's technical/typewriter type, `+ UNIVERSES` | Consistent shared-shell nav |
| **LAUNCH PAD button** | Top-right, distinct call-to-action styling | **Resolved as the PDF export/download action** (§11a) — "launch" fits the archival/space-faring language site-wide (sending the dossier out into the world), and gives the page's one hard real-world requirement a clear, discoverable entry point rather than burying it. Flagging this as the interpretation being built against — worth a quick confirm since the mockup doesn't state its function explicitly. |
| **Cursor** | `QuillCursor` — a small quill/pen-nib cursor | Doubles as the mechanism for the UV-light discovery easter egg (§10) in V2 |
| **Dossier Metadata box** | `UNIVERSE: EARTH-PRIME · ARCHIVE ID: GA-2026-07 · CLASSIFICATION: PUBLIC DOSSIER · STATUS: AUTHENTICATED · LAST UPDATED: [date]` | The `LAST UPDATED` field pulls automatically from the content file's own last-modified data (§11) — never hand-typed, so it can't silently go stale |
| **Wax seal** | A red (dark mode: amber) wax seal stamp bearing your initials, positioned near the metadata box and again at the document's close | Purely decorative but load-bearing for the "authenticated recovered artifact" framing — appears at both the top (opening) and the closing "END OF DOSSIER" footer |

---

## 3. Entry — The Unfolding Sequence

Per your original brief: `Portal Opens → Ancient Case Appears → Wax Seal Breaks → Document Unfolds`. This is Dossier's contribution to the shared transition system (`universes/dossier/transitions.ts`), composed with whatever exiting effect the previous universe declares — not a bespoke one-off:

1. **Portal opens** — reusing the shared `portal` transition effect already established in the site-wide effects library (Projects' mirror uses the same effect; Blog's ASCII-decode reuses `glitch`/`collapse` — this universe's contribution is `portal` + a new `paperUnfold` effect).
2. **A case/document container appears**, as if something physical has just arrived — with a small optional verification flourish on its surface (a brief terminal-style readout, e.g. `VERIFYING SUBJECT... GARV_ARORA — STATUS: VERIFIED`), fast and understated rather than a dramatic full-screen sequence of its own.
3. **The wax seal visibly breaks** — a small, satisfying snap.
4. **The document unfolds** into the page's actual layout — panels/sections settling into place as if the paper itself is being smoothed flat.

Respects `prefers-reduced-motion`: collapses to a straightforward cross-fade into the resolved layout, skipping the case/seal/unfold staging (§0 scopes this as a simplified V1 version regardless — the full multi-stage choreography can be refined once the core page is stable).

### 3a. Closing — End of Dossier (adopted, mirrors the opening)

The dossier currently opens with real ceremony (§3) but simply stops after the last section — worth closing with matching weight rather than trailing off. A final sheet, visually distinct (slightly darker/more worn than the rest, as if it's the oldest or most-handled page):

```
END OF DOSSIER

STATUS: ACTIVE
LAST VERIFIED: [date, same source as §2's Dossier Metadata]

DOCUMENT STILL BEING WRITTEN
```

Followed by a handwritten-style signature, the closing wax seal (§2 already establishes this appears here), and the `LAUNCH PAD` PDF export action (§11a) as the natural final beat — the document closes, and the one thing left to do is take a copy of it with you. `STATUS: ACTIVE` and `DOCUMENT STILL BEING WRITTEN` matter emotionally, not just as page dressing — they're what keeps a recovered-artifact framing from implying "this person is done," when the actual truth is closer to "this is current, and still unfolding."

---

## 4. The Physical Paper System

Every section of this page should read as a distinct physical sheet, not a seamless webpage:

- **Curled corners and torn edges** — achieved via `clip-path` polygons and layered edge-texture images, not literal 3D geometry.
- **Depth between sheets** — layered `box-shadow` (and a subtle background sheet peeking out from behind the front one) wherever sections visually stack, reinforcing "these are separate documents, gathered together" rather than one continuous scroll.
- **Coffee stains, tape, paint spots** — placed deliberately, not randomly scattered — a stain or piece of tape should feel like it's *marking* something (see §10's coffee-stain easter egg), not just decorating empty space.
- **Paper grain texture** — a subtle noise/grain overlay across the whole page, consistent between light and dark mode (adjusted for the charred-paper dark variant, §12).

---

## 5. The Technical Blueprint Portrait — The Page's Hero Artifact

Replaces a normal headshot with what your brief describes as "Da Vinci reverse-engineering you": a profile sketch annotated with dimension markers (`1780 mm`, `640 mm` in the existing mockup), construction lines, and coordinate markers — presented as a technical drawing of a person, not a photo.

**This is Resume's centerpiece, the same way the face mesh is Home's** — currently it exists as a section among others; it should dominate instead. Desktop layout: the portrait occupies roughly **40% of the initial viewport**, large and unmissable, with the remaining 60% holding the Dossier Metadata box, tagline, and the new Current Status sheet (§5a) beside it. When someone thinks back on this page later, the blueprint portrait should be the specific image that comes to mind — the same way Home's rotating face is what people remember from that universe.

- **Static/entrance state (V1):** the fully-resolved technical sketch, present on load, at hero scale.
- **Dynamic Ink (V2):** as the visitor scrolls past this section, the construction lines and dimension annotations draw themselves progressively — implemented via animated SVG `stroke-dashoffset` (the standard technique for a "hand-drawn" reveal), scroll-linked so the sketch feels like it's actively being drafted rather than simply fading in.

### 5a. Current Status Sheet (new)

The dossier framing risks implying "this is a historical record" — this section is the fix, proving the document is current rather than archived-and-finished. Placed directly beside the hero portrait, styled as a smaller sheet **pinned on top with a strip of tape**, looking visibly newer/less worn than the surrounding pages, as if it was added recently:

```
CURRENT STATUS

LOCATION
Vellore, India

ROLE
Final Year, Computer Science & Engineering — VIT

CURRENT OPERATIONS
Samsung PRISM — Data Science Intern
R&D Lead — SEDS Projects VIT

SEEKING
Robotics Engineering · Embedded Systems · Autonomous Systems · Research
```

This creates the bridge the page currently lacks — past achievements → current state → what's next — and it's cheap to build (one more small sheet using the same physical-paper styling as everything else, §4) while meaningfully changing how "alive" the whole page feels.

---

## 6. Documents-as-Sections (the "different documents" system)

Per your original brief's "make sections look like different documents, not one giant page" — the six numbered sections already established in the mockup each read as a **visually distinct sheet type**, stacked together like a gathered case file rather than one continuous page:

1. **Experience Log** — a timeline-styled sheet, each entry with a small icon, role, dates, and bullet points. Each entry carries a small file-ID (`RECORD_001`, `RECORD_002`...), consistent with the archive-ID conventions already established for Blog posts and Projects' patent files — a cheap, consistent detail, not "MISSION_XXX" framing (which reads closer to the rejected militaristic tone above). **Internal structure, adopted:** each entry breaks into `OBJECTIVE` / `RESULT` / `TECH STACK` — a clean, scannable three-part shape borrowed from a "mission report" proposal, kept here without the military naming that came with it. The structure is good on its own merits; the label wasn't necessary to get it.
2. **Technical Competencies — a decision point, not yet fully resolved:** organized into sub-groups (Robotics, Software, Hardware & Embedded, Tools & Frameworks, Design & Creative). The confirmed mockup shows a simple bar/level indicator per skill; **the case against bars (implying false precision — "Python: 85%" doesn't mean anything concrete) has now been raised independently twice**, which is a real signal, not noise, even though it conflicts with approved visual reference. Rather than resolve this unilaterally a second time from inside a spec document, treat it as a genuine pre-production decision: prototype both — the confirmed bar version, and the alternative categorized-spec-list format (skills grouped under each category as plain labeled text, styled like a technical parts list rather than an infographic) — and pick based on which actually reads better once built, not on document consensus alone.
3. **Recovered Records** — the Achievement Stamps (§8), including a dedicated elevated treatment for the two real patents (§8a).
4. **Education** — institution details alongside a small architectural sketch of the building, consistent with the technical-drawing visual language used elsewhere.
5. **Verified Credentials** — certifications, each styled like a small certificate/seal rather than a plain list item.
6. **Leadership & Impact** — a list-style sheet with small star icons, with one entry (§8b) elevated to a hierarchy/org-chart visual for your highest-weight leadership role.

Each section's sheet-styling (paper color/texture variant, corner-fold direction, whether it looks slightly more worn or crisp) can vary subtly per section — reinforcing that these are gathered documents of different ages and origins, not six copies of one template.

### 6a. Timeline Rail (persistent section navigation)

A persistent left-hand rail (desktop only — mobile's accordion, §13, serves the same purpose there) listing all six sections; the current section highlights automatically as the visitor scrolls (a standard scrollspy pattern). This is a genuinely useful addition this page was missing — the equivalent of Blog's reader Outline panel (§6 of the Blog spec), giving a long single-page document a real sense of "where am I" without needing to scroll back up.

---

## 7. Engineering Sketch Overlays

Faint, semi-hidden background diagrams — kinematic chain equations, PID control diagrams, sensor fusion sketches, rover wheel calculations — layered subtly behind the main content, almost hidden rather than prominent. **Cross-universe consistency opportunity:** rather than commissioning entirely new diagrams for this page, reuse the same technical sketches already established for Projects' Research Board and whiteboard (§5.6/whiteboard content in the Projects spec) — the same equations and diagrams appearing faintly here as they do prominently there reinforces that this is the same person's real technical work being referenced across universes, not two separate art directions inventing similar-looking but unconnected sketches.

**V1 scope:** one or two hero placements (behind the portrait, behind the Mars rover sketch). **V2:** extend to more sections once the core page is proven — wallpapering every section with overlays before the base content is solid would be polish arriving before substance.

---

## 8. Achievement Stamps ("Recovered Records")

Replaces conventional badges with **wax seals, stamps, and signatures** — deliberately collectible-feeling, per your original brief. Each stamp represents one real achievement: TEDx Speaker, School President, Smart India Hackathon Finalist, Mars Rover Builder, World Record Holder, and similarly weighted real accomplishments.

**This is the third rendering of the same underlying achievement data**, not a separately-authored list: Home's Achievement Constellation, Blog's `relatedContent` references (a Reference Block pointing `→ RESUME: Patent Filing`, per the Blog spec), and this section all read from the same `content/achievements.json` (§11). Keeping this consistent means updating an achievement once — a new stamp appears here, a new star appears in Home's constellation, and it becomes referenceable from Blog — without hand-maintaining three separate copies of the same fact.

Each stamp, visually: a circular wax-seal or rubber-stamp graphic with the achievement name, a small icon, and the year — clicking or hovering could reveal a slightly expanded description, but the stamp itself should be legible and meaningful even collapsed, since a recruiter scanning quickly shouldn't need to interact with anything to understand what's being claimed.

**Classification tags (adopted):** each stamp also carries a small category label — `RECOGNITION`, `WORLD RECORD`, `RESEARCH`, `LEADERSHIP`, `INDUSTRY` — giving the Recovered Records section real visual hierarchy and scannability at a glance, beyond just a wall of similarly-weighted stamps.

**Tiered visual weight (adopted):** not every achievement should read as equally significant, and treating them that way actually makes the section harder to scan, not easier. Three tiers, expressed purely through stamp size:
- **Tier A (large seals):** World Record, patents, TEDx — the handful of accomplishments that are genuinely rare or hard to replicate.
- **Tier B (medium seals):** School President, team leadership roles — real, but more common among strong candidates.
- **Tier C (smaller stamps):** individual certifications, smaller recognitions.

This is a cheap change (one size variable driven by a `tier` field on the achievement data, §11) with real payoff — a recruiter scanning for five seconds sees the biggest things first, literally.

### 8a. Patents, Elevated (new)

Your two real patents (the 5-DOF Gesture-Controlled Robotic Arm and the mmWave Earthquake Survivor Detection Robot) deserve treatment distinct from — and more prominent than — a general achievement stamp. Each renders as a small **patent file**, styled like an engineering drawing rather than a wax seal: a miniature technical sketch of the invention, a reference/filing number, filing date, a brief abstract, and status (`Filed` / `Application Pending`), consistent with the technical-drawing visual language used for the portrait (§5) and the sketch overlays (§7). If future patents are added, they follow the same file format automatically — no redesign required, consistent with the data-driven rendering principle in §11.

### 8b. Leadership Hierarchy (new, selectively applied)

Most Leadership & Impact entries stay simple list items with a star icon, as already established. **One exception, deliberately:** your highest-weight leadership role (R&D Lead overseeing two competition teams and 50+ engineers) is worth a small hierarchy/org-chart visual — `SEDS Projects VIT → R&D Lead → 2 Teams → 50+ Engineers` — because scale is the actual story there, and a plain bullet undersells it. This isn't a treatment every leadership entry needs; applying it everywhere would make simpler entries (e.g. a single hackathon mentorship) feel artificially inflated.

---

## 9. Dynamic Ink (scroll-driven drawing animation)

Per your brief: as the visitor scrolls, sketches draw themselves, annotations appear, and signatures write themselves — rather than content simply fading or sliding in. Technique: animated SVG `stroke-dashoffset`, scroll-linked via the same `lib/motion/scroll.ts` utilities used elsewhere on the site, not a bespoke one-off animation system for this page alone.

**V1:** applied to the portrait (§5) as the primary showcase of this effect. **V2:** extend to the Mars rover sketch, section header underlines, and the closing signature at the document's end (§0) — each additional application is cheap once the underlying scroll-linked-stroke mechanism is proven once.

---

## 10. Discovery Mechanics (V2) — and Visible Field Notes (V1, a different thing)

**Genuinely hidden mechanics from your original brief and since** (V2, discovery-based, all must remain fully skippable):

- **UV-light cursor reveal:** while the `QuillCursor` (or a special toggled mode) passes near certain areas, faint hidden text becomes legible — as if the visitor is holding an ultraviolet light over old paper. This should reveal small, genuine bonus content (a short aside, a hidden note) — never anything a recruiter actually needs to see to evaluate you, consistent with every other universe's rule that hidden/easter-egg content is flavor, never gatekeeping.
- **Coffee stain → secret project:** clicking a specific coffee stain (§4) reveals a small hidden project or note — not a random Easter egg for its own sake, but tied to something genuine (an early or abandoned project, similar in spirit to Projects' Research Board patent reveals or Blog's Ghost Posts).
- **A redacted document:** a blacked-out/censored-looking entry somewhere on the page — opening it reveals something self-aware and human rather than a real secret: *"TOP SECRET: still figuring life out."* Fits the warm, self-aware tone this universe is meant to carry, distinct from the rejected militaristic framing (§0) — this is a joke about the "classified dossier" conceit, not an attempt to actually feel like classified material.
- **A `clearance` command-palette keyword:** typing `clearance` into the site-wide command palette while on this page reveals a small set of hidden annotations — consistent with the `~about` terminal convention already established for About Me and Blog, giving this universe its own variant of the same site-wide "type something to unlock a bit more" pattern rather than inventing an unrelated mechanic.

**A separate, deliberately distinct thing — visible field notes (V1, not hidden):** a handful of small handwritten margin annotations that are simply *there*, not discoverable — a few, sparingly placed, not attached to every entry:
- *"Built this prototype in 14 days."*
- *"This almost failed."*
- *"Still my favorite project."*

These aren't easter eggs; they're a visible design element that makes an otherwise-polished page feel personal, distinct from the hidden mechanics above, but always in view rather than requiring discovery. Keep this genuinely sparse — a note on every entry stops feeling human and starts feeling like a template field.

Every hidden mechanic above must be fully skippable — the document reads completely and correctly to any visitor (or any screen reader) who never discovers any of them.

---

## 11. Content Data Model & Update Workflow

**This is deliberately not a full CMS, unlike Blog.** Resume content changes a handful of times a year (a new role, a new certification, an updated skill) — building a dedicated authoring UI for that cadence would be solving a problem this page doesn't actually have. Instead:

- Content lives in structured files already established in the locked architecture's `content/` folder: `content/experience.json`, `content/skills.json`, `content/achievements.json`, `content/education.json`, `content/credentials.json`.
- **Updating the resume means editing these files directly and redeploying** — since the page is SSG (static, no live backend), a new commit with an updated JSON file and a redeploy is the entire "update workflow." This is appropriate given the update cadence, not a corner being cut.
- **Shared data, not resume-exclusive:** `content/experience.json` and `content/achievements.json` are the same files already powering Home's Achievement Constellation and Blog's `relatedContent` references (§8) — Resume doesn't own this data, it's one of three consumers of it.
- **`LAST UPDATED` in the Dossier Metadata box (§2)** reads the file's actual last-modified metadata rather than a hand-typed date, so it can never silently drift out of sync with reality.
- **V2, if manual JSON editing ever becomes genuinely tedious:** a minimal single-purpose admin form (reusing Blog's existing `/admin` auth) for editing these specific fields — but this is explicitly not a V1 concern, and may never be worth building if updates stay infrequent enough that direct file edits remain comfortable.

### 11a. PDF Export (hard requirement, not flavor)

Unlike every other universe, this page needs to produce something a recruiter can download, print, and forward — the creative experience is a presentation layer over a document that must also work as a normal resume. The `LAUNCH PAD` button (§2) triggers this export.

- **Recommended approach:** a genuinely separate, clean PDF template — not a screenshot or print-stylesheet of the animated page. Generate it server-side from the same structured content (`content/experience.json` etc.) using a real PDF-generation library, so the exported document is professional, ATS-readable where relevant, and entirely independent of the page's visual theme. The dossier aesthetic is for the browsing experience; the PDF is for actually applying to jobs.
- **The download itself is a small ritual, not just a file save (adopted):** clicking `LAUNCH PAD` plays a brief, satisfying sequence — the folder visually closes, a stamp marks it `EXPORTED`, then the actual PDF download begins. Cheap (a short animation gating a standard download trigger), and it makes downloading feel like taking a real file from the archive rather than clicking a generic export button.
- This is worth treating as seriously as any other V1 item in this spec — it's arguably the single most functionally important piece of the entire Resume universe, even though it's the least visually interesting one.

### 11b. Cross-Universe References (already-built alignment, confirmed)

This universe's data already flows through the same `relatedContent` relationship engine established for Blog (§9a of the Blog spec) and referenced from Home's Achievement Constellation — no new mechanism needed here, just concrete examples of it in use on this page specifically. A `RELATED UNIVERSES` link list can appear beneath relevant entries:

- **Mars Rover Builder** (achievement) → `PROJECTS: Mars Rover` · `BLOG: Why We Built It`
- **mmWave Survivor Detection patent** → `PROJECTS: Earthquake Bot` · `BLOG: Lessons From Field Testing`
- **R&D Lead / leadership entry** → `ABOUT: Leadership Philosophy`

This isn't new work triggered by this spec; it's confirmation that Resume's content model was already designed to be one more consumer of the shared engine, not a fourth separately-invented relationship system.

---

## 11c. Sound Design

A gap in the original draft of this spec, worth closing explicitly — physical, restrained sounds only, consistent with the "recovered document" framing (no sci-fi or futuristic UI sounds anywhere on this page):

| Interaction | Sound |
|---|---|
| Entering the universe (§3) | The wax seal's snap, paper unfolding |
| Opening/expanding a section | A soft folder-opening sound |
| Hovering a stamp or document | A subtle paper rustle |
| Scroll-triggered Dynamic Ink (§9) | A faint, sparse pen-scratching sound, timed loosely to the drawing animation |
| Downloading the PDF (§11a) | The stamp sound, timed to the `EXPORTED` mark |

Governed by `audioStore` category volumes, consistent with every other universe.

---

## 12. Dark Mode

Already confirmed by existing mockups, not a proposal: dark mode shifts from sun-bleached brown paper to **charred black paper with amber/orange ink**, and edge damage shifts from worn/sun-faded to burned/charred. Same content, same layout, same physical-paper system (§4) — just a different decay story, consistent with the "own personality per mode" principle established site-wide (paired here with a literal narrative justification: this dossier looks like it survived a fire in one reality, sun exposure in the other).

---

## 13. Mobile Adaptation

Confirmed by the existing mobile mockup: the six document-sections (§6) become a **vertically stacked accordion** — each section collapsed by default, showing an icon, a one-line description, and a `VIEW [SECTION] →` expand action. Portrait, Dossier Metadata, and the wax seal remain visible at the top regardless of which sections are expanded. This is the natural mobile equivalent of "gathered documents you can leaf through," rather than trying to force the desktop's stacked-sheet visual metaphor into a cramped viewport.

---

## 14. Recruiter Mode (V2)

A real, practical addition addressing this universe's core tension directly: PDF export (§11a, V1) already solves "a recruiter needs something to download and forward," but doesn't help someone who wants to quickly scan the page itself without leaving it. Recruiter Mode is a toggle (near `LAUNCH PAD`) switching the in-page rendering between:

- **Dossier Mode** (default) — everything in this spec: paper physicality, wax seals, stamps, sketch overlays, animations.
- **Recruiter Mode** — the same underlying data (`content/experience.json` etc.), rendered as a clean, plain document: no paper effects, no stains, no overlays, no scroll animation. Just Experience, Skills, Education, Achievements, in a straightforward readable layout.

**Why this fits the architecture rather than fighting it:** this is a third renderer over data already structured for exactly this purpose — the same "one data source, multiple presentations" principle already used for Blog's Orbit/List views and `PostRenderer`, applied here as Dossier-view vs. Recruiter-view instead of Orbit vs. List. It's real engineering work (a genuinely separate, simpler layout template), which is why it's scoped to V2 rather than launch — but it's bounded work, not a new architectural pattern to invent.

---

## 15. What Not To Build / Accessibility Guardrails

Consistent with the standing-guardrail pattern established for Blog, worth stating explicitly here too, since this universe has a real-world stake other universes don't:

- **Do not let the physical-paper aesthetic compromise actual readability.** Every section is real semantic HTML (proper headings, lists, real text) underneath the paper styling — a recruiter skimming quickly, or a screen reader user, gets the same information a sighted browsing visitor does, just without the sketch-drawing flourish.
- **Achievement Stamps (§8) need real text alternatives** — a decorative wax-seal graphic is not sufficient on its own; the achievement name, year, and brief description must exist as real accessible text, not only as an image.
- **Discovery mechanics (§10) must never gate essential content** — this is already stated above but worth repeating as a hard rule: nothing a hiring decision depends on should live only behind a hover-triggered UV-light effect or a coffee-stain click.
- **`prefers-reduced-motion`** disables Dynamic Ink's scroll-drawing (falls back to a simple fade-in) and simplifies the entry sequence (§3) to a cross-fade — content is unaffected either way.
- **Do not let this page's creative ambition slow down the one interaction that matters most:** a visitor should be able to find your actual experience and skills quickly, without needing to wait through unfolding animations first. If the unfolding sequence (§3) ever starts to feel like it's in the way of a fast, functional read, that's reason enough to shorten or simplify it — this page's job is to be read, not just admired.

---

## 16. Performance Notes

- This universe stays **outside the R3F budget** and outside the "real backend" budget both — no WebGL, no live database, no auth-gated editor. It's SSG content rendered from static JSON files, consistent with the architecture doc's original assessment of Resume as one of the lower-technical-risk universes.
- Since there's no live backend, there's no ISR/ISR-revalidation concern the way Blog has — a content update is a redeploy, not a database write.
- Paper-texture and grain overlays should be lightweight (CSS/SVG, not large raster textures) to keep this page's Lighthouse performance high, especially since it needs to load quickly for a recruiter who may not wait through a heavy asset pipeline before deciding whether to keep reading.
