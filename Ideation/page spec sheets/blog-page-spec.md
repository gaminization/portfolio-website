# Blog Page — Brutalist Archive Universe — Complete Spec

Universe ID: `brutalist-archive` · Routes: `/blog`, `/blog/[slug]`, `/admin/*` (editor) · Reference: `universes/brutalist-archive/*`

The emotional pitch: this isn't a blog feed, it's **an archive drifting in space** — raw, monospace, unpolished by design, but orbiting in something that feels vast and cosmic rather than flat. Where Projects is warm and tactile, Brutalist Archive is stark, honest, and a little cold — thoughts and experiments left in orbit for anyone willing to explore them.

**A content rule, not a feature — worth stating plainly since it costs nothing to implement and does more for distinctness than most of the systems below:** each universe already answers a different question about you, and Blog's posts should be written with this in mind rather than duplicating what another universe already covers — **Projects shows what was built (and how); Resume shows what was achieved; About Me shows who you are; Blog explains why it mattered.** A post that just re-describes a project's technical details is redundant with Projects — the version worth writing is the one only Blog can tell.

---

## 0. V1 / V2 Prioritization

Given the pattern established on Projects, this universe gets the same discipline applied from the start rather than accumulated and cut later. This page is unusual among the six universes in that it also includes a real backend and a full authoring tool — read this section first.

**V1 — Launch Critical**
- Archive home in **Orbit** and **List** view modes (§4), the post windows themselves, the ASCII-decode open transition (§5), the full Individual Post Reader layout (§6), the core editor with text/code/image/note/divider/table blocks and autosave (§8), Postgres-backed publishing (§9), single-admin auth gate, and basic search.
- Movable notes are readable and author-placed at minimum; visitor drag-to-play (§7) is a cheap, low-risk addition worth including in V1 since it's client-side/ephemeral only.
- **Post Types (§8a):** the `postType` schema field ships in V1, with Essay and Mission Log fully realized; Research Note/Build Log/Archive Fragment's specific treatments wait for V2.
- **Lightweight cross-universe relation links (§9a):** `relatedContent` is the single relationship engine every future graph visualization (Archive Map, Innovation Graph) will consume — cheap to collect now, and nothing collected is wasted once a visualization gets built later.
- **Reference Block (§8):** a new editor block type that inserts a structured pointer into `relatedContent`, rendered as an archive-styled callout — the natural authoring surface for the relationship data above.
- **`postStatus` lifecycle (§9):** `thought/testing/active/completed/archived/superseded` — one field, surfaced automatically across Orbit, reader, search, and Related Posts, including a small visual glyph per status in Orbit (§9).
- **Post Birth Sequence (§8):** the brief compile→stamp→ASCII-thumbnail→launch-into-orbit animation on publish — cheap, and directly reinforces the "living archive" feeling.
- **Orbit Rings (§4.1):** the core/middle/outer placement driven by existing `featured`/`postType` metadata — a one-time lookup, not a physics system, so it's V1-compatible even though topic clustering (a real physics system built on top) stays V2.
- **ASCII thumbnails (§6a):** the same publish-time conversion pipeline that already produces the full illustration also produces a small thumbnail reused across Orbit/search/related posts — no separate system, just an additional output size.

**V2 — Recommended, Not Required for Launch**
- **Public comments** (§6) — moved here deliberately. Every other V1 item directly reinforces the archive metaphor; comments don't. The spec already documents real moderation/approval/notification overhead this would require — if anything needs cutting when implementation begins, this is the first candidate, not an afterthought to trim later.
- **Grid and Timeline view modes** (Orbit + List alone cover both the signature experience and the accessible baseline).
- **Custom data-visualization/diagram blocks** beyond a generic configurable chart block, and 3D model embeds in posts.
- **Full version history UI** (autosave itself is V1; browsing/restoring old versions is V2 — a later proposal suggested treating version history as launch-critical, but autosave alone already covers the actual risk it mitigates — data loss — so the browse/restore UI on top is genuinely deferrable).
- **The full template library** (ship with one or two templates, not all five, for V1).
- **Topic-based window clustering in Orbit** (§4.1) — a real force-directed layout system, distinct from and built on top of the V1 ring placement above.
- **Ambient background atmosphere** (§3a) — drifting text fragments, ghost windows — nice, but needs its own visual reference before an artist builds it.
- **Archive Weather** (§3a) — a further enhancement to the already-deferred ambient layer above; sequence after it, not alongside.
- **The Archive Map / cross-universe knowledge graph** (§9b) — a visualization of the relationship engine (§9a), deferred alongside Projects' Innovation Graph since both consume the same underlying data and shouldn't be built as separate systems.
- **Granular discovery-progress stats** (§11b) — deferred until the features they measure (Ghost Posts, the Archive Map) actually exist.

### What Not To Build (a standing guardrail, not a one-time decision)

This universe is strongest specifically because it never tries to become a content platform. Worth stating explicitly, since these kinds of features tend to creep in individually over time, each seeming small in isolation: **do not add** reactions, likes, claps, bookmarks, reading streaks, achievement badges, or an article recommendation engine. The moment any of these ship, "a collection of thoughts drifting in orbit" starts reading as "Medium, Substack, or LinkedIn with a different skin." If a future feature request sounds like it belongs on a social content platform, that's reason enough to say no here, regardless of how cheap it'd be to build.

---

## 1. Visual & Style Direction

Primary register: **brutalist print design** — raw monospace type (IBM Plex Mono or similar), high-contrast black/white base palette, visible grid lines, minimal ornamentation, un-precious typography (deliberately "unfinished" feeling headers, exposed structure). Layered on top of this: a **cosmic/orbital backdrop** — the archive exists in deep space, not on a page. The combination is the whole point: austere print design floating in an infinite void, not either extreme alone.

**Light mode is a genuinely different material, not a recolor** — already confirmed as the strongest light/dark execution across the site: dark mode is a black terminal/archive; light mode flips to a cream paper, blue-ink, red-accent zine/print aesthetic. The emotional register for light mode specifically: **a research archive recovered from a laboratory** — windows read as physical documents/folders/research sheets rather than screen-native UI, and the ASCII illustrations read as printed terminal logs rather than live digital renders. Push the physical details further: stamps, handwritten correction marks, folded paper corners, the occasional coffee ring, a handwritten arrow pointing at something — small tactile imperfections that make it feel genuinely recovered rather than a clean digital mockup of paper. This deliberately echoes the Resume/Dossier universe's physical-artifact language leaking into Blog — a nice, cheap piece of cross-universe visual consistency. Same brutalist bones and the same confirmed color palette, just a different physical medium (recovered paper vs. active screen), consistent with this universe's "own personality per mode" principle established site-wide.

---

## 2. Persistent / Global Elements

| Element | Description | Behavior |
|---|---|---|
| **Top bar** | `UNIVERSE: BRUTALIST_ARCHIVE` tag with a small `1999` marker, `EARTH-PRIME.LOG` readout, standard six-item nav re-skinned in monospace | Consistent shared-shell nav |
| **Cursor** | `PixelCursor` — a small blocky/pixelated crosshair | Status readout at the bottom of the screen literally states `CURSOR: ORBITAL` — a nice diegetic confirmation that the cursor itself is "in orbit" with everything else |
| **Command palette** | Persistent bottom bar: `COMMAND PALETTE CTRL+K` / `type a command or search...` | Searches posts, tags, and categories via Fuse.js (§10); also reachable via the site-wide `⌘K` shortcut, sharing the same underlying command registry as every other universe |
| **Sound toggle** | Waveform icon, bottom-left | Controls this universe's ambient track |
| **Status readout** | Bottom-right: `STATUS: EXPLORING` / `STATUS: READING...` (changes contextually between archive view and an open post) | Small, honest diegetic detail — the site telling you what mode it thinks you're in |

---

## 3. Archive Home — Structure Before View Modes

The archive's actual data (all posts, their metadata, categories, tags) is view-mode-independent — Orbit, Grid, List, and Timeline (§4) are four different **renderers** over the same underlying post list, not four different data sources. This matters for both engineering (one query, four presentational layers) and content (nothing is orbit-exclusive or list-exclusive).

**Sidebar (persistent across view modes):**
- **View Mode selector** — radio-style toggle: `ORBIT` / `GRID` / `LIST` / `TIMELINE`.
- **Filters/Categories** — `ALL · ROBOTICS · SOFTWARE · DESIGN · LINUX · LIFE · RANDOM · ARCHIVE`, each with a live post count.
- **Search** — a plain text input, powered by the same Fuse.js index the command palette uses.
- **Shortcuts** — `[K]` command palette, `[H]` help, `[N]` new note (author-only, hidden for visitors), `[M]` map view.

**Right sidebar:**
- **Archive Map** — a small radar/orbit-style visualization showing `X% articles discovered` with a progress bar. This is blog-specific "exploration" tracking, distinct from the site-wide Multiverse Navigator's universe-level `% explored` — this one tracks which *posts* a visitor has actually opened, giving the archive its own small sense of progress. **A real design tension, raised twice now, worth taking seriously rather than dismissing:** the argument is that percentages nudge visitors toward completionist/collectible-hunting behavior rather than curiosity, and that Blog specifically — an archive, not a game — should favor qualitative language (`RECENTLY UNCOVERED` / `CONNECTIONS FOUND` / `LAST VISITED`) over a raw number. The reasoning is sound, and being raised a second time is a genuine signal, not noise. Holding the position from before regardless: the percentage framing is already established site-wide (the Multiverse Navigator's `% EXPLORED`, Home's identical hero-band readout) — changing it in Blog alone would fix one universe's psychology while leaving the same concern live in Home and the Navigator, and would make Blog inconsistent with the pattern used everywhere else. This is worth escalating as a genuine site-wide architecture question (does the *whole site* move away from percentages, not just Blog) rather than resolving piecemeal, one universe at a time. Until that site-wide call is made, the compromise stands: keep the percentage, pair it with a curiosity-framed line alongside it (e.g. a rotating `RECENTLY UNCOVERED: [post title]`).
- **Time filter** — `All Time / This Year / This Month / This Week / Today`.

### 3a. Ambient Background Atmosphere (recommended, unconfirmed by any existing mockup)

Beneath the post windows, a deeper background layer can give the void real texture rather than flat black — worth building, but flagged explicitly as a recommendation rather than something shown in approved concept art, since it would need its own visual reference before an artist builds it:

- A very faint, near-invisible grid — like an endless blueprint sheet, only noticeable on close attention.
- Small text fragments drifting slowly through the space — `draft_203`, `unfinished`, `prototype` — evocative of an archive with far more inside it than what's currently on display.
- Faded "ghost window" outlines with no content, occasionally fading in and out — a suggestion of archived or unfinished thoughts without literally exposing them.

Keep this layer genuinely subtle — it's meant to reward attention over time, not compete with the actual posts for a first-time visitor's focus.

**Archive Weather (V2, and only after this base layer exists):** once the ambient background above is built, it could subtly shift based on recent content — more grid lines and "system noise" if recent posts skew technical (robotics, research, engineering), softer and more ghost-note-heavy if recent posts skew personal. This is a nice enhancement layered on an already-deferred feature, not a reason to build the base layer sooner — sequence it after, not alongside.

---

## 4. View Modes

### 4.1 Orbit (default, V1 — the signature experience)

Post "windows" are arranged in a loose, roughly spherical/globe-like formation — not a literal 3D scene (this universe stays outside the "minimal R3F" budget established site-wide; the orbit effect is achieved with CSS 3D transforms/perspective over a 2D layout, not WebGL). Windows drift with a very slow, continuous ambient motion — nothing dramatic, just enough that the archive reads as "alive" rather than a static grid pretending to be spherical.

- **Post windows** are styled like real OS windows (title bar with `_ □ X`, a filename like `POST_042.TXT`) — deliberately reusing About Me's OS-window visual language, since both universes share the "your files, your archive" framing even though their aesthetics differ completely.
- **Draggable, cosmetic-only:** visitors can drag windows around within the orbit view, exactly like About Me's desktop icons — repositioning is purely visual and doesn't affect publish order or any other visitor's view; it resets or persists per the same session-storage pattern already established for About Me.
- **Sticky notes** float among the windows — handwritten-style fragments (*"Don't overthink. Build. Ship. Iterate. Repeat."*, *"Ideas are cheap, execution is everything."*) — author-placed during writing (§7), not tied to any single post.
- **Featured post** renders larger/more central than the others, with a `FEATURED` tag.
- **Orbit rings (V1, simple metadata-driven placement):** rather than an unstructured drift, windows sit in one of three loose rings — a **core ring** (featured posts, slow-moving, large windows), a **middle ring** (normal posts), and an **outer ring** (Archive Fragments, small, drifting more freely, sometimes partially obscured). This placement is driven entirely by existing metadata (`featured` flag, `postType`) — no physics simulation, no clustering algorithm, just three fixed radius bands a post's own data already determines. As the archive grows from a handful of posts to dozens, this structure gives Orbit a sense of scale without ever needing a redesign.
- **Topic clustering (V2, genuinely different from the rings above):** posts sharing tags/categories could gently gravitate toward each other over time (robotics posts drifting near other robotics posts, forming loose constellations by topic) — a real force-directed layout system running *within or across* the rings above, not a small addition to the drift animation. Worth building once V1's ring-based Orbit is stable, not before. (Don't conflate this with the rings — rings are a one-time metadata lookup; clustering is a live physics system.)

### 4.2 Grid (V2)

A straightforward card grid — same post data, no orbit physics. Useful as a calmer alternative once built, but not required to launch alongside Orbit and List.

### 4.3 List (V1 — the accessible baseline)

A plain, fully semantic list of posts — title, excerpt, date, category, read time — with zero spatial/physics dependency. This is not an afterthought bolted onto Orbit; it's the guaranteed, fully-accessible way to browse the archive, and should be built to the same completeness as Orbit, just without the presentation layer on top.

### 4.4 Timeline (V2)

Posts ordered strictly chronologically, presented as a simple vertical timeline. A nice-to-have once the archive has enough posts to make chronology meaningful.

---

## 5. Opening a Post — The ASCII-Decode Transition

This is the direct realization of your original brief's "brutalist page that expands into ASCII when clicked" — and it's also this universe's contribution to the site-wide transition system (`universes/brutalist-archive/transitions.ts`), reusing the same effect-composition model established in the architecture doc rather than a one-off animation:

1. **Tap/click** the post window.
2. **Flatten** — the window's content briefly compresses/flattens, as if losing a dimension.
3. **Pixelate** — the flattened content breaks into a coarse pixel/dot-matrix pattern.
4. **ASCII-form** — the pixelated image resolves into actual monospace ASCII characters, briefly readable as "art" before further resolving.
5. **Reader arrives** — the full Individual Post Reader (§6) settles in, itself opening with its ASCII illustration already in the resolved state established by the transition (the transition and the post's own decorative ASCII art are the same visual language, not two unrelated effects).

Respects `prefers-reduced-motion`: collapses to a straightforward cross-fade between the archive and the post, skipping the flatten/pixelate/ASCII-form staging.

---

## 6. Individual Post Reader

- **Header:** `FEATURED` tag (if applicable), large title, a one-line subtitle/dek, then a meta row — date, read time, category, status.
- **ASCII illustration:** a decorative piece rendered in dot-matrix/ASCII style (e.g. a mountain silhouette with a small figure) — **pre-baked at publish time, not live-converted per visitor.** The editor's image block (§8) includes an optional "render as ASCII" toggle that runs a one-time brightness-sampling conversion when the author uploads the source image, storing the result as part of the post's content — this avoids wastefully re-running image-to-ASCII conversion in every visitor's browser and keeps the art directable (the author can see and adjust the result before publishing, rather than trusting an automatic per-visitor render).

### 6a. ASCII as a Core Identity System, Not Just Decoration

The same one-time conversion pipeline above should also produce a **small thumbnail variant**, not just the full-size in-post illustration — reused consistently across every surface that shows a post preview: the Orbit window itself, search results, Related Posts, and (eventually) the Archive Map (§9a). One conversion, multiple output sizes stored together — not a separate thumbnail-generation system to maintain alongside the full illustration. This is what makes ASCII read as this universe's core visual identity rather than a one-off flourish only visible inside individual posts.

Small additional touches that reinforce the same identity cheaply: ASCII-style section dividers (`================`, `<> <> <> <>`) instead of plain horizontal rules, and Mission Log's status-panel motif (§8a) as a recurring, recognizable piece of the archive's visual language.
- **Body:** numbered sections (`01_ SLOW IS A SUPERPOWER`, `02_ COLLECT. CONNECT. CREATE.`) rather than plain unbroken prose — matches the mockup's structured, almost technical-report feel. Includes the occasional highlighted block-quote line and a pull-quote with attribution, styled consistently with the brutalist type system (not a generic blockquote style borrowed from elsewhere).
- **Scroll progress, rendered as literal ASCII:** a bottom-bar progress indicator made of actual text characters filling in as you scroll (alongside a numeric `SCROLL: XX%`), plus a live `LINES / WORDS / CHARS` counter — small, cheap, thematically perfect details that reinforce "this is a text file/archive object," not decoration borrowed from elsewhere.
- **Right sidebar:** `POST_META` (ID, status, date, read time, type, mode, an `ORIGINAL_DOCUMENT` link), `TAGS`, `OUTLINE` (the numbered sections as a jump-nav — auto-generated from the post's actual headings, not hand-maintained), `RELATED_POSTS`.
- **Footer:** prev/next post navigation, share icons (X/LinkedIn/email/copy-link).
- **Comments (V2, moved deliberately):** the `comments` table exists in the locked schema, so it's technically easy to add — but on reflection, comments are the one piece of this universe that doesn't reinforce the archive metaphor the way everything else does, and they carry real moderation overhead (spam, abuse, an approval workflow, notifications) that every other V1 feature avoids entirely. If built later: comments should be visible only after a lightweight admin approval step (a `pending/approved` status field), with an email notification to you on new submissions — but this whole feature is now explicitly V2, not a launch requirement.

---

## 7. Movable Notes (Virgil Abloh–style annotation layer)

**Scope clarification, resolved deliberately:** a later proposal suggested making the *entire* post a freeform canvas — every block (text, image, video, diagram) carrying absolute x/y/width/height/rotation coordinates, not just notes. That's rejected here, not for scope-discipline reasons but a real technical one: absolute-position layouts don't have a sensible answer for responsive/mobile rendering (a block placed at a specific pixel coordinate on a 1400px screen has no natural equivalent on a 375px phone) or for screen-reader reading order (a scattered canvas has no inherent sequence). The resolution below gets the "designed composition, not a rigid template" feeling without that problem:

- **At authoring time:** the editor's Note block can be placed and dragged freely anywhere on the post canvas — not confined to the linear block flow — letting an author scatter short, quotation-mark-style annotations across a post the way a designer might annotate a layout. This is a genuinely freeform placement, not just "a sticky-note-styled block that still lives in the vertical flow."
- **Notes as real marginalia, not just decoration:** beyond free text or a quote, a note can optionally carry a link — to another post, a Projects object, an achievement, or a resume entry (reusing the same lightweight `relatedContent` reference type described in §9). This makes the annotation layer genuinely useful cross-referencing, not purely visual flourish.
- **Everything else stays order-based, not position-based:** text, image, video, and diagram blocks remain in a normal, reflowable sequence — this is what actually survives responsive resizing and stays screen-reader-navigable. Images and video can carry a small optional **visual offset** (a slight left/right nudge within their slot, like a magazine pull-image) rather than true freeform canvas coordinates — enough to feel designed, not enough to break on a phone.
- **At reading time:** a note's author-set position is its default, but **visitors can drag notes around for fun** — this is cheap and low-risk to include (position is client-side/ephemeral only, resetting on reload, never written back to the server), and it's a small, delightful, on-brand interaction rather than a real content-editing risk.

---

## 8. The Custom Block Editor

A dedicated authoring tool, gated behind `/admin/*` and a single-admin auth check (Auth.js) — not a public-facing feature. Built on **Tiptap** with custom Node extensions, per the locked architecture, rather than a from-scratch ProseMirror wrapper.

**Layout (three panes):**
- **Left — Insert Panel:** block types (Text, Code, Image, Video, Note, Divider, Table, Terminal, Checklist, Diagram, 3D Model) plus **Templates** (V1: ship with one or two — e.g. Blank and Mission Log — rather than the full five-template library; add Field Report/Tutorial/Research Note once the core editor is proven).
- **Center — Canvas:** the post itself — title, subtitle, category, tags, and visibility fields at the top, then the block sequence (or freeform-positioned blocks, per §7) below. A rich-text toolbar (bold/italic/strike/code/headings/lists/quote/link/image/table/hr) appears contextually.
- **Right — Meta Panel:** an auto-generated Table of Contents (mirrors what readers see in §6's Outline), Quick Notes (author-only scratch notes attached to the draft, not necessarily published), Attachments (uploaded files with size), and tag suggestions.

**Block types, specifically:**
- **Text** — standard rich text.
- **Code** — syntax-highlighted, matching the archive's monospace visual language natively rather than looking like a bolted-on generic code widget.
- **Image** — includes a required alt-text field (accessibility, non-negotiable) and the optional ASCII-render toggle (§6).
- **Video** — drag-and-drop upload.
- **Note** — the movable annotation block (§7).
- **Divider, Table, Checklist** — standard structural blocks.
- **Terminal** — renders as a small fake-terminal transcript, consistent with the terminal motif used across About Me and Projects.
- **Diagram (V1, generic) / 3D Model (V2, specific):** rather than hand-building bespoke one-off data-visualizations per post (the mockup's "LIDAR VISUALIZATION" block is a good example of a custom widget, but building a unique component for every future post's specific data would not scale) — ship V1 with **one generic, configurable Diagram/Chart block** (simple line/bar/node-graph rendering driven by data the author supplies), and treat fully custom visualizations or embedded 3D models as V2, built only when a specific post genuinely needs one.
- **Reference (V1, new):** inserts a structured pointer into the relationship engine (§9a) — a Project, Achievement, Resume entry, or another Blog post — rendered not as a plain hyperlink but as a small archive-styled callout. Cheap to build (it's a UI wrapper around data that already exists), fits the archive aesthetic exactly, and is the most direct way an author actually populates `relatedContent` while writing, rather than that field being an abstract backend concept with no natural authoring surface. **The label prefix is destination-aware** — the same underlying reference renders differently depending on `targetType`, so the callout itself signals which kind of universe it's pointing to rather than a generic "reference" tag: `→ PROJECT: Gesture-Controlled Robotic Arm`, `→ RESUME: Patent Filing`, `→ ABOUT: Robotics.iso`, `→ BLOG: Why I Built It`. One block, one data shape, four small rendering variants — not four different reference block types to maintain.

**Mechanics:**
- **Drag-and-drop reordering** via `dnd-kit` — keyboard-operable, consistent with the site's accessibility requirements.
- **Autosave (V1):** debounced writes to the backend as the author types.
- **Version history (V2):** autosave itself is V1; a UI for browsing and restoring past versions is a V2 addition on top of the same underlying snapshots.
- **Markdown shortcuts** (typing `##` for a heading, etc.) and **live preview** — a toggle between `PREVIEW (ORBIT MODE)` and `PREVIEW (READ MODE)`, letting the author see the post exactly as it will appear in both the archive card and the full reader before publishing.
- **Publish flow:** `SAVE DRAFT` → `PREVIEW` → `PUBLISH → ORBIT` — the button's own label ties the action directly to the result (publishing sends the post into the orbit view), reinforcing the metaphor rather than using a generic "Publish" label.
- **Post Birth Sequence (V1, cheap, high thematic payoff):** publishing isn't an instant database write from the visitor's perspective — it plays a brief, satisfying sequence: the window compiles, its metadata stamps itself, **it's assigned a permanent archive ID** (e.g. `POST_084.TXT`, `MISSION_021.LOG` — following the same filename convention as its post type, §8a), its ASCII thumbnail generates (§6a), then the window visibly launches into orbit, with existing windows subtly shifting to make room. That archive ID, once assigned, is permanent and appears everywhere the post is referenced — the Orbit window, the reader's `POST_META` sidebar (§6), search results, Related Posts, and any Reference Block (above) pointing to it — giving the archive a real sense of permanence rather than posts being identified only by an internal, invisible database slug. This whole sequence is genuinely cheap (an entrance animation, one ID assignment, and a small one-time position nudge to nearby windows — not a live physics simulation) and it's what makes publishing feel like adding something to a living system rather than inserting a row into a table.
- **Reusable blocks (V2):** saving a configured block (e.g. a specific diagram setup) as a reusable snippet for future posts — a nice modularity win once the core editor is stable, not something to build before it.

## 8a. Post Types (merged with Templates, not a separate system)

Not all posts should read the same way — a robotics build log and a personal essay have genuinely different natural shapes. Rather than building Post Types as a separate classification system alongside the editor's Templates above, **they're the same choice made once**: selecting a template when creating a post sets its post type, which then informs both the default block arrangement and the reader-side presentation treatment.

| Post Type (= Template) | Example content | Default treatment |
|---|---|---|
| **Essay** (= Blank) | *Why I Build Things*, *Creativity and Engineering* | Typography-focused, minimal media, closest to the archive's plain brutalist reading register. Orbit window: large, stable, center-weighted — visually anchoring the core ring (§4.1) it belongs in — filename convention `ESSAY_014.TXT`. |
| **Mission Log** | *Building a ROS2 Teleop System*, *Patent Journey* | Diagram- and telemetry-screenshot-friendly, structured around numbered sections (matches §6's existing numbered-section body style), and can include a small ASCII-styled status panel (`SYSTEM STATUS / CPU: ACTIVE / BUILD: COMPLETE / ERRORS: 0`) as a concrete recurring motif for this type specifically. Orbit window: telemetry-styled chrome with visible status indicators, filename convention `MISSION_021.LOG`. |
| **Research Note** | *Visual SLAM Notes*, *IMU Fusion Experiments* | Citation/figure-friendly, more formal structure. Orbit window: denser, more technical-looking chrome, filename convention `RESEARCH_007.DAT`. |
| **Build Log** | Week-by-week development journal entries | Chronological progress-update structure, room for documenting failures/lessons as first-class content, not just a postscript. Orbit window: includes visible checkpoint/version indicators (e.g. `┌ BUILD_018`). |
| **Archive Fragment** | Tiny artifacts, not full posts — see below | Deliberately small — renders as a mini archive entry rather than a full post. Orbit window: tiny, weathered, peripheral — drifting in the outer ring (§4.1) rather than anchored like Essay's core-ring placement — sometimes appearing partially folded, filename convention `FRAGMENT_112.MEM`. |

**A note on terminology:** "starting block arrangement per type" has been proposed under three different names across three rounds of feedback — Templates, Post Types, and "Saved Compositions." These are the same concept. Keeping it as one system: selecting a type/template when creating a post is the single decision that sets the starting blocks, the reader-side treatment, *and* the Orbit window's visual shape above — not three separate systems to keep synchronized.

**Archive Fragment, elevated to a real content type, not just a smaller visual style:** these are archival debris, not short posts — a single dated entry, a one-line lesson, an unresolved half-thought:

```
2025-11-03
"Spent 8 hours debugging.
The bug was a missing semicolon."
STATUS: RESOLVED
```

```
ROVER_LOG_014
Today the robot drove into a wall.
Progress.
```

```
FRAGMENT_071.MEM
Maybe curiosity is a skill.
```

Because these are meant to be spontaneous and tiny, they need their own lightweight authoring path — a quick-capture entry point (a small modal or a dedicated shortcut) rather than opening the full three-pane editor for one line of text. Forcing a full editor session for a fragment this small would defeat the point of it being quick, low-friction archival debris.

**Why this matters for Orbit's rings:** once Archive Fragments are a real, frequently-populated content type, the Orbit rings (§4.1) stop being a placement mechanism for a handful of full posts and start making the archive feel genuinely larger and older than its actual article count — the outer ring fills with small, scrappy artifacts rather than sitting comparatively empty next to the core and middle rings.

**V1 scope:** ship with Essay and Mission Log fully realized — both their reader-side treatment and their distinct Orbit window shapes — consistent with the "one or two templates for V1" scope above. Research Note, Build Log, and Archive Fragment's specific treatments (reader-side and Orbit shape both) wait for V2 — though Archive Fragment's quick-capture authoring flow is cheap enough that it's worth building alongside V1 if time allows, since it directly strengthens Orbit's sense of scale from day one. The `postType` schema field itself is cheap to add now regardless of how many types have bespoke rendering at launch — better to have the field from day one than migrate it in later.

---

## 9. Backend & Data Model

Per the locked architecture (no separate CMS app, no MDX files — this genuinely needs structured, block-based storage):

- **Database:** Postgres (Supabase/Neon) via Drizzle. The `posts` table stores each post's content as **structured JSON** (Tiptap's native document format) in a `content jsonb` column — not raw HTML, and not Markdown files.
- **Why JSON, specifically:** storing structured JSON (rather than pre-rendered HTML) means the **same content can be rendered two different ways** — a `PostRenderer` component reads the identical JSON document and produces both the brutalist archive-card excerpt (Orbit/Grid/List views) and the full ASCII-styled reader view (§6). This is the direct answer to "how do posts become part of the design" — the design isn't baked into the content at write-time; the same data is interpreted differently depending on where it's being displayed. (A later review independently re-derived this same principle — a post's metadata, not hand-authored presentation, determines its size/ring/style/status-indicators wherever it appears, across Orbit, List, Related Posts, Search, and the eventual Archive Map — which is a good sign this is the right architecture, arrived at twice from different directions.)
- **Schema:** `posts` (id, slug, title, content, category, tags array, **postType enum** [§8a], status draft/published, publishedAt, readingTime, timestamps), `comments` (id, postId, author, body, status pending/approved, createdAt), plus the site-wide `contactSubmissions` table (unrelated to blog, shared database).
- **`postStatus` lifecycle (V1, new):** beyond the simple draft/published state above, technical posts benefit from a richer lifecycle field — `thought / testing / active / completed / archived / superseded`. This is one metadata field with several outputs: it surfaces automatically in the Orbit window's chrome, the reader's meta row (§6), search results, and Related Posts — a post's status shouldn't need to be separately maintained in four different places. **In Orbit specifically, expose this aggressively, not subtly** — a small glyph on the window itself, since the field already exists and this is essentially free: `● green pulse` for active, `▲ amber` for testing, `□ gray` for archived, `↺ red` for superseded. Cheap, and it does more to make Orbit feel like a living archive than most of the more elaborate systems in this spec.
- **Cross-universe relations (V1, lightweight):** a small additional field on `posts` — `relatedContent`, a simple array of typed references (e.g. `{ type: 'project', id: 'rover-track' }`, `{ type: 'achievement', id: 'tedx-speaker' }`, `{ type: 'resume-entry', id: 'seds-rover-team' }`) — surfaced on the post reader (§6) as plain text links ("Related: Projects → Rover Track"). This is genuinely useful and cheap enough for V1: it's just reference data and a rendered link list, **not** the full graph-visualization engine described below, which stays V2.
- **Rendering strategy:** ISR (Incremental Static Regeneration) for both `/blog` and `/blog/[slug]` — fast repeat visits and good SEO, with new/edited posts appearing without a full site redeploy.
- **Auth:** Auth.js, single-admin gate — you are the only author, so this doesn't need multi-user roles or permissions, just a real login wall in front of `/admin/*`.
- **Images/attachments:** stored via Vercel Blob or Supabase Storage, referenced by URL in the post's JSON content. (A later proposal suggested Cloudflare R2/AWS S3 for storage and Meilisearch/Postgres full-text search for search — both are reasonable technologies in general, but they're additional infrastructure providers beyond what's already locked for a personal, single-author blog at modest post volume. Keeping the simpler locked stack — Vercel Blob/Supabase Storage, client-side Fuse.js — unless post volume or traffic genuinely outgrows it later; revisit then, not preemptively.)
- **Schema stays simple, deliberately:** the same proposal suggested a more normalized schema (`post_tags`, `post_relations`, `drafts`, `widgets` as separate tables) — that's reasonable for a multi-author CMS at scale, but is more structure than a single-author blog needs. The locked schema's `content jsonb` + `tags` array + a `status` enum already covers this; if version history (§0, V2) is built later, a simple snapshot table is enough — no need for a fully separate relational graph.

### 9a. One Relationship Engine, Not Several (formalized)

By this point, "how does a piece of content connect to the rest of the portfolio" has been independently proposed as at least four differently-named systems across different reviews: Projects' Innovation Graph, this universe's Archive Map, the `relatedContent` field already in this spec, and — in a later round — "Threads/References/Knowledge Links." **These are all the same underlying data shape**, and should be built once:

```
{ sourceType, sourceId, targetType, targetId, relationType }
```

**`relatedContent` (§9, already V1) *is* this engine** — not a Blog-specific convenience that happens to resemble it. Every future graph-style visualization — Projects' Innovation Graph, this universe's Archive Map, or any other that comes later — should consume this same relationship data, not define its own schema. The sequencing principle worth locking in explicitly: **build the relationship engine first, visualizations second.** Building Archive Map and Innovation Graph as separate V2 efforts before the underlying relationship data model is solid would mean rewriting both once the shared engine actually gets built — better to have V1 already collecting the right shape of data than to retrofit it later.

**Two things explicitly not adopted, and why:**
- **"Threads"** — proposed as a new way to connect related content by topic, but this is just Tags (already in the schema, §9) under a different name. Categorization is already solved; renaming it doesn't address a new problem.
- **"Archive Memory"** (welcome-back messaging, last-visit tracking, "new posts since you were here") — a reasonable idea, but it's a cross-universe concern, not a Blog-specific one. If built, it belongs in the shared shell layer (per the architecture doc) so it works identically everywhere, not as something reinvented inside this universe alone.

### 9b. The Archive Map (V2 — a visualization of the engine above, not a separate system)

For Blog specifically, the relationship engine surfaces as a hidden **Archive Map** — reachable via a keyboard shortcut (`Ctrl+Shift+M`, or a `MAP` command-palette entry) rather than a permanent nav item, keeping it a discoverable layer rather than a required feature. It renders posts, projects, achievements, and universes as connected nodes, reading directly from `relatedContent` (§9) — no separate dataset of its own. **Firmly V2**, sequenced after the relationship engine itself is proven in V1, alongside Projects' Innovation Graph (same underlying data, different universe-specific presentation).

---

## 10. Search

- **Client-side, via Fuse.js** — indexes post titles, excerpts, tags, and categories. No backend search infrastructure needed at this content scale.
- **Shared with the command palette** — the same Fuse.js index powers both the archive sidebar's search box and the site-wide `⌘K` command palette's post-search results, so there's one index to maintain, not two.

---

## 11. Sound Design

| Source | Sound |
|---|---|
| Ambient (archive-level) | A soft CRT hum layered with faint, distant modem noise and a touch of tape hiss — deliberately period-appropriate to the `1999` archive marker, colder than Home or Projects' ambience |
| Hovering a window | A soft paper-shuffle sound, and the window's drift motion stabilizes/stops — reinforcing that hover brings it to readable focus |
| Opening a post | A brief digital "decode" sound, timed to the ASCII-transition stages (§5) |
| Dragging a window or note | A soft mechanical click/shuffle |
| Command palette open | A short terminal-style blip |

Governed by `audioStore` category volumes, consistent with every other universe.

---

## 11a. Ghost Posts (given a dedicated section, since this keeps being flagged as needing "a home" — it already has one, just making that unambiguous)

Rare archive objects — not indexed, not searchable, not listed in List View. Only discoverable through exploring Orbit. A small, fixed set: roughly 3–5 total, not an open-ended or generated pool, with usually only one or two visible at a time:

- `UNPUBLISHED_002` — *"I don't think I'm ready to write this one yet."*
- `REDACTED_004` — *"[DATA CORRUPTED]"*
- `TODO_017` — *"Finish this after graduation."*

**These appear only in Orbit, never in List View** — this is what keeps them feeling like discoveries rather than content, and it's an acceptable exclusion specifically because Ghost Posts carry no essential information (pure flavor/personality, not something a visitor needs to do anything with) — consistent with how other universes' pure easter eggs (About Me's Recycle Bin joke, Projects' coffee mug clicks) also aren't required to be equally reachable through each universe's accessible fallback view. If a screen reader user does encounter one via Orbit, it should still be marked up as real, readable text — not an image — so the discovery itself is accessible even though it isn't indexed.

**These aren't easter eggs for the sake of easter eggs** — they reinforce the archive's core idea: not every thought becomes a finished article. That's worth remembering when deciding whether to expand this set later — the value is in what they represent (unfinished, abandoned, half-formed), not in having more of them.

**Content guidance for whoever writes these:** the specificity is what sells it — a real-feeling metadata line (`status: abandoned` / `last modified: 03:12 AM`) and a genuinely unresolved ending (a few honest lines, like *"I thought this idea would work..."*, then nothing — no conclusion, no tidy wrap-up) reads as far more human than a polished-sounding "abandoned idea" placeholder would.

## 11b. Other Easter Eggs

- **A hidden abandoned-drafts detail** somewhere in the archive (a faded, unclickable-looking window that turns out to open something small) — in the same spirit as Projects' quieter easter eggs. Keep this list short and well-made rather than exhaustive, consistent with the discipline already applied on Projects — a couple of genuine surprises land better than many thin ones.

### Discovery Progress (V1 simple, V2 expanded)

- **V1:** the Archive Map widget's existing `% articles discovered` (§2) is the whole feature — simple, already spec'd, no additional systems required.
- **V2, once the features it depends on actually exist:** a more granular breakdown — `Articles Read: 12/38`, `Secrets Found: 3/11`, `Connections Discovered: 28/102`, rolling up into an overall `Archive Completion: 24%`. This is appealing but shouldn't be built ahead of what it's measuring — "Secrets Found" only makes sense once Ghost Posts/easter eggs are countable, and "Connections Discovered" only makes sense once the Archive Map graph (§9b) exists. Scope this stat expansion to track its dependencies, not to ship early and sit mostly at zero.

---

## 12. Mobile Adaptation

Confirmed by the existing mobile mockup:

- Orbit's spherical arrangement doesn't translate to a small screen — mobile defaults to a **vertically stacked list of post windows**, keeping the window-chrome visual language (title bar, filename) without the 3D-ish physics.
- The Archive Map widget persists, showing the same `% discovered` progress.
- Individual posts keep their full layout (meta, tags, outline, related posts, share icons, prev/next, and comments once built in V2) in a single scrollable column.
- Bottom tab bar provides primary navigation, consistent with every other universe's mobile pattern.

---

## 13. Accessibility Notes Specific to Blog

- **List view (§4.3) is the primary accessible path** — fully built to the same completeness as Orbit, not a stripped-down fallback.
- **ASCII illustrations need real alt text**, describing the actual image content — decorative-looking text art is still a meaningful image and must not be skipped by screen readers or left with empty/placeholder alt attributes.
- **`prefers-reduced-motion`:** disables Orbit's ambient drift and collapses the ASCII-decode transition to a simple cross-fade (§5) — content itself is unaffected either way.
- **Movable notes:** must be fully readable in their default position without requiring drag interaction — dragging is a delight layer on top of, not a requirement for, reading the content.

---

## 14. Performance Notes

- This universe stays **outside the R3F budget** — Orbit's spherical arrangement is CSS 3D transforms over a standard DOM layout, not a WebGL scene, consistent with the site-wide "minimal R3F, mostly artwork" principle (only Home and Projects use real R3F).
- ISR keeps the archive and individual posts fast without requiring a full rebuild per new post.
- Pre-baked ASCII art (§6) avoids any per-visitor client-side image-processing cost.
