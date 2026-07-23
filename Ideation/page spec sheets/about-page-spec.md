# About Me Page — Aero-OS Universe — Complete Spec

Universe ID: `aero-os` · Route: `/about` · Reference: `universes/aero-os/*`

Unlike Home, About Me isn't a scroll narrative — it's a **spatial desktop metaphor**. There's no `narrative.ts` for this universe (§3a doesn't apply here); instead the page is one persistent "desktop" the visitor inhabits, with windows they can open, drag, stack, and close, exactly like using someone's actual computer. The whole point is that it should feel like you've been let into a real, slightly messy, personal desktop — not a stylized "about" section wearing an OS skin.

**Structural decision, made deliberately:** this page stays a static desktop with **no scroll**. An earlier alternative proposed the desktop transforming through infinite vertical scroll (Desktop → Documents → Memories → Dreamscape), but that was rejected — it would make About Me behave like a second scroll-narrative page in an OS costume, duplicating the pattern Home already owns (bands, `narrative.ts`), and it would create two homes for the same content (a "Memory Gallery" alongside Memories.zip; a "Personal Interests Zone" alongside the Things I Like sticky note). The "the deeper you dig, the more personal it gets" feeling this was chasing is achieved instead through **nested folder drill-down inside windows** (§5.1's Journey folders, §5.5's Robotics.iso) — genuinely digging deeper, just spatially within a window rather than scrolling across the page.

**Naming correction to apply before any copy is finalized:** earlier mockups reference "Aryaman Singh" in the terminal/bio text — this is a leftover placeholder. Every instance should read **Garv Arora**.

---

## 1. Visual & Emotional Direction

Aesthetic: **Frutiger Aero / Windows XP–Longhorn / early Mac OS X webcore** — glassy, optimistic, slightly nostalgic-futurist. Glossy taskbar, Aero-glass window chrome (translucent blur, soft highlight along the top edge), rounded window corners, a wallpaper that looks like early-2000s "default wallpaper" energy (sweeping vista, dramatic sky) but rendered with a more advanced, futuristic skyline — the aesthetic is "the future, as imagined by 2005."

The emotional target: curiosity and warmth, not spectacle. Where Home is bold and declarative, About Me should feel quiet and a little intimate — like you've been invited to look through someone's actual files.

---

## 1a. Boot Sequence (entry from Home)

When the transition into About Me completes, it doesn't drop straight into the desktop — it plays a brief boot sequence first, giving Aero-OS its own signature "arrival" the way Home's card-selection animation gives Universe Gateway one:

- Screen reads `AERO OS` / `Version 3.14`, with a progress bar filling beneath a sequence of loading lines: `Loading Memories...` → `Loading Curiosity...` → `Loading Ambition...` → `Loading Dreams...`
- Duration: roughly 1.5 seconds — brief enough not to feel like a real wait, long enough to register as a deliberate beat.
- A small classic startup chime plays.
- This is Aero-OS's contribution to `universes/aero-os/transitions.ts`'s `entering` array — composed with whatever exiting effect the *previous* universe declares, per the shared transition machine (§4 of the architecture doc), not a bespoke one-off animation living outside that system.
- Respects `prefers-reduced-motion`: the boot sequence shortens to a near-instant fade with the text appearing all at once, rather than a full animated loading sequence.

---

## 2. Persistent / Global Elements

| Element | Description | Behavior |
|---|---|---|
| **OS title bar (top)** | Doubles as both Aero-OS's branding and the shared universe nav — reads `ABOUT_ME.OS` / `v2.0 // BUILDING MY FUTURE`, with the standard six-item nav (`Home · About Me · Projects · Blog · Resume · Contact`) styled as native OS toolbar buttons rather than a typical website nav bar | The shared shell's `Navigation.tsx` is re-skinned via this universe's `theme.ts`, not replaced — same underlying nav, different chrome |
| **Custom cursor** | `BubbleCursor` skin — a small glossy bubble with a soft highlight, matching the Aero aesthetic | Shifts to an open-hand "grab" cursor over any window's title bar, and a closed "grabbing" fist while actively dragging |
| **Sound toggle** | Same global control, positioned within the OS system tray (bottom-right, next to a fake clock) rather than floating separately | Consistent placement logic per universe — here it belongs in the tray because that's where a real OS puts it |
| **System tray clock** | A live clock reading the visitor's actual local time, styled like a real OS clock | Small „is this really live?" delight detail — cheap to implement, adds to the "real computer" illusion |
| **CRT effects overlay** | A subtle, toggleable post-process layer: faint scanlines, slight bloom around bright UI elements (window highlights, the PowerDVD equalizer), a touch of chromatic aberration at the screen edges, and a very slight barrel-curvature vignette | Applied as a single screen-space CSS/SVG filter layer, **not** baked into individual components — one `<CRTOverlay />` sits above everything. Toggleable independently in the Accessibility Panel (distinct from `prefers-reduced-motion`, since this is a visual-comfort preference, not strictly a motion one) and automatically disabled if `prefers-reduced-motion` or `prefers-contrast` is set |

---

## 3. The Desktop (Background Layer)

The wallpaper is not a static image — it's a shallow-depth illustrated scene with its own quiet ambient life, always present behind the windows:

- **Scene:** a futuristic city skyline at dusk/night, seen from a rooftop ledge — dense towers, a soft-glowing river winding through the base of the city, a moon (or small distant planet) in the sky, one or two sleek aircraft/drones drifting slowly in the distance.
- **The figure:** Garv sits cross-legged on the ledge in the foreground, wearing over-ear headphones, looking out at the skyline. This is the "animation of me sitting in the corner" from the original brief — rendered as a subtle idle-loop animation, not a static illustration:
  - A slow, barely-perceptible breathing motion (chest/shoulders).
  - A rotating set of small common idle behaviors: an occasional head-nod in time with the music, a sip from a drink, a brief stretch, glancing around.
  - **Rare, low-frequency animations** (each appearing only once every few minutes, so they read as genuine surprises rather than an obvious loop): dropping a pen and picking it back up, reaching over to "fix" something off-screen, a longer stretch.
  - **Cursor-tracking:** the figure's head subtly turns toward the cursor's position when it moves near the figure — the same "notices you" language established for Contact's astronaut in your original brief, applied consistently here. Small and understated, not a full head-follow.
  - **Window-awareness (specific, not generic):** rather than a generic "glances at whatever's open," each window gets its own small, distinct reaction, so attentive visitors notice the figure is actually responding to *what* they've opened, not just *that* something is open — a head-nod synced to the beat when PowerDVD is playing (returning to idle when paused), a small smile when Robotics.iso is open, a slight lean-back when Football.mp4 is open. Each reaction is rare and brief, not a held pose for as long as the window stays open.
- **Ambient background motion (parallax, not full 3D):** the distant aircraft/drones drift slowly across the skyline on a loop; the moon/planet very slowly arcs; a faint shimmer plays across the river's surface. All implemented as flat parallax layers (`lib/motion/parallax.ts`), **not** an R3F scene — Aero-OS stays entirely in the "artwork + CSS/GSAP" budget, consistent with the site-wide "minimal R3F" decision (only Home and Projects get real 3D).
- **Depth cue:** the desktop icons and windows sit on a layer above the wallpaper with a very subtle drop shadow, reinforcing that they're "on top of" the glass/screen, while the wallpaper itself sits slightly further back with a touch of depth-of-field softness at the very back layer (the furthest towers/clouds).

### 3a. The Wallpaper Is a Living System (not a scroll layer)

The wallpaper carries real interactive depth of its own — but entirely through **hover-discovery and live state**, never through scroll. (An earlier proposal suggested a "scroll depth system" where the desktop drifts upward on scroll to reveal a hidden memory layer underneath — this is explicitly rejected: it reintroduces the exact scroll-narrative pattern this universe was built to avoid, just relocated beneath the desktop instead of after it. The two mechanics below get the same "there's more here than it looks" feeling without it.)

**Hover-discovery (static, always available):** when most/all windows are minimized and the wallpaper is more visible, hovering specific elements in the scene reveals a small caption:
- The orbiting rover/satellite → *"Mars Rover Prototype"*
- A distant satellite → *"Mission Control Link Available"* — a deliberate, quiet tease of the Projects universe's hidden ROS2 dashboard, a third thread alongside `secret.exe` (§9) and the Robotics.iso content.
- A city building → *"Ideas currently under construction."*
- Garv himself → *"...still figuring things out."*

**State-reactive (dynamic, tied to what's actually happening on the desktop):**
- Few windows open → the city reads quieter, dimmer, more distant.
- Many windows open → city lights brighten slightly, the scene feels more active.
- PowerDVD playing → river reflections pulse gently in time with the music; aircraft lights blink a touch more.
- Terminal open → a few additional small satellites become visible in the sky.
- `secret.exe` discovered → one distant building lights up distinctly — the Mission Control tease made visible on the wallpaper itself, not just inside the easter-egg window.

All of this is subtle by design — the goal is a desktop that quietly feels alive and responsive to what the visitor is doing, not a wallpaper doing its own competing show.

---

## 4. Desktop Icons

Arranged in a single vertical column, left edge — classic OS convention:

| Icon | Label | Opens |
|---|---|---|
| Person silhouette | `My Story.exe` | The main bio/identity window |
| Soccer ball | `Football.mp4` | A short "why I love football" aside — video or a styled text+image card |
| CD/disc | `Robotics.iso` | A robotics-focused personal note — distinct from the Projects universe; this is "why robotics matters to me," not project case studies |
| PS/design icon | `Design.psd` | A short design-philosophy aside |
| Music note | `Music.mp3` | Shortcut that brings the PowerDVD window to focus, or opens it if closed |
| Zipped folder | `Memories.zip` | The photo-carousel window |
| Text document | `Future.txt` | A simple text-editor window — green blinking cursor, text (future goals: further study, research directions, dream projects/companies) types itself out gradually rather than appearing all at once |
| Recycle Bin | `Recycle Bin` | An easter egg — see §9 |

**Interaction:**
- Single click/tap: selects the icon (a soft highlight box appears around it), matching real-OS conventions.
- Double-click (desktop) / tap (touch, single-tap acts as the "open" gesture, no double-tap requirement — touch conventions differ from desktop and shouldn't force a desktop-only gesture): opens the window with a quick scale+fade "materialize" animation, originating from the icon's position rather than just appearing centered.
- Icons can be dragged and rearranged within the column — entirely cosmetic (position isn't meaningful data), but it's a small "this is a real desktop" affordance that costs little to implement (`dnd-kit`, same library already used for the blog editor's blocks).

**Desktop clutter (authenticity detail):** real desktops aren't this tidy. Rather than adding several more top-level icons (which would make the column visually overwhelming), consolidate this into one additional icon — a loosely-organized `Downloads` folder — that opens to a small, slightly messy file list:
- Joke filenames every engineer will recognize: `robot_arm_v34_final_FINAL.zip`, `robot_arm_v35_final_really_final.zip`.
- A handful of random screenshot thumbnails.
- A `notes.txt` containing a half-finished thought — *"remember this idea —"* trailing into something intentionally slightly nonsensical.
- An `Untitled Folder` that, when opened, is genuinely empty or contains one small joke — nobody (including you) quite remembers what it was for.

This is pure flavor, not a content section that needs real information architecture — keep it small and finish it quickly rather than let it become its own production effort.

---

## 5. Windows

Every window shares the same chrome: an Aero-glass title bar (translucent, soft top-edge highlight), minimize/maximize/close buttons (top-right, XP/Longhorn styling), and a drop shadow that intensifies slightly while the window is being dragged (reinforcing "lift"). All windows are draggable by their title bar, resizable is **not** required (keeps scope sane — these are fixed-size "documents," not general-purpose resizable panels), and clicking anywhere on a window brings it to the front (z-index management via a single `zIndexStore`-style ordering, not per-window state).

### 5.1 My Story.exe (primary window, open by default)

The identity/bio centerpiece — a left sidebar + main content pane, styled like a simple file/help-viewer application:

**Sidebar sections:** `OVERVIEW · WHO AM I · JOURNEY · VALUES · VISION · IDEAS · RANDOM.TXT`

- **Who Am I** (default-selected pane): a short first-person bio. Draft direction pulling from the real resume rather than placeholder copy — e.g. *"I'm Garv Arora. A robotics and embedded systems engineer who'd rather ship something that moves than talk about moving it — ROS2 navigation stacks, ARM firmware, autonomous rovers, and the occasional patent along the way."* (Treat this as a starting draft for you to edit into your own voice, not final locked copy — the emotional register should stay yours.)
- **Journey**: rather than a flat scrolling narrative, this is where the "dig deeper" idea lives — a small set of folders (`Childhood`, `Leadership`, `Technology`, `Creativity`, `Today`), each opening to reveal a short story fragment plus a photo or sketch. Pulls from the same `content/experience.json` that powers Resume's Experience Log and Home's Achievement Constellation — one shared data source, presented here in a more personal, browse-at-your-own-pace register than Resume's formal dossier framing.
- **Values**: a short list of guiding principles (3-5 short statements) — genuinely needs your input; don't let this ship as generic placeholder ("integrity, passion, excellence") — it should sound like something only you would actually say.
- **Vision**: a brief forward-looking statement — where this is all headed.
- **Ideas**: a rotating list of smaller brainstorm fragments/interests — can be a lighter, half-finished-thought register, contrasting with the more polished Who Am I/Journey panes.
- **Random.txt**: an easter egg, not a real content section — clicking it generates a new random quip/fact/joke each time (a small pre-written array, or fun placeholder logic like a rotating "fortune cookie"), refreshing on every click without needing a page reload.
- **Footer:** a small quote strip, present regardless of which sidebar item is active — *"The best way to predict the future is to build it."* — with a tiny globe icon, a quiet nod to the "multiverse" framing without breaking the desktop illusion.

### 5.2 CyberLink PowerDVD (music player)

A faithful pastiche of the early-2000s CyberLink PowerDVD skin — brushed-metal/glass player chrome, a **live-reacting equalizer/waveform visualizer** (not a static image), transport controls (prev/play-pause/next), a scrub bar with elapsed/remaining time, and format badges (Dolby Digital / DTS / DVD-Video / MP3) purely as retro decoration.

- Track shown: `Journey.mp3` — playing, with an artist credit line beneath.
- The waveform visualizer should genuinely animate in sync with (or a convincing simulation of) the actual audio playing, not loop independently of it — mismatched visualizer/audio is an easy way to break the "this is real" illusion.
- Play/pause actually controls Aero-OS's ambient audio track — this window is not decorative, it's the real audio control surface for the page, tied into `audioStore`.
- **PowerDVD as the page's emotional soundtrack, not just a music toy:** a small playlist selector lets the visitor pick a mood, and each choice makes a subtle, coordinated shift across wallpaper tint, ambient sound, and the desktop figure's behavior (tying directly into §3a's state-reactive wallpaper) — e.g. a cooler blue tint and slower idle animation for a "focus" mood, versus a warmer tint and slightly more energetic idle behavior for a "late night builds" mood. **Scope this to 2-3 playlists for v1** (not a full six-mood roster) — the mechanic matters more than the quantity, and it's easy to add more moods later once the underlying tint/sound/behavior-coordination system is proven to work with one or two.

### 5.3 "Things I Like" sticky note + Polaroid

- A yellow sticky-note-styled panel, slightly rotated, with a pushpin: `Things I like:` followed by a short list — Robotics, Football, Designing, Music, Space, Linux, Problem Solving — closing with a small hand-drawn smiley.
- Beside it, a taped Polaroid-style photo — a symbolic image rather than necessarily a literal personal photo (a night road, a piece of landscape, whatever best captures a personal mood) — physically "taped" at a slight angle, consistent with the scrapbook/physical-object language established on Home.
- Neither of these is a draggable "window" in the OS sense — they're desktop decorations, fixed in place, though they could still cast the same soft depth-shadow as the real windows for visual consistency.

### 5.4 system_info.sh (terminal)

A small terminal window running a fake `about_me --info` command, styled like a real Linux terminal (monospace, green-on-black or similar), outputting:

```
name: Garv Arora
role: Robotics & Embedded Systems Engineer
constellation: Builder
fuel: Curiosity + Caffeine
mission: Build the future
status: In Progress...
```

- Text should type itself out character-by-character on first load (a classic terminal-boot flourish), then leave a blinking cursor at an empty prompt.
- **This is also the entry point for the site-wide terminal easter egg** (per your original brief's "~ opening a terminal to interact" idea) — this window can accept real keyboard input. A visitor typing `help` gets a short list of fake commands (`whoami`, `sudo make me a sandwich`, `cat achievements.txt`, etc.), each returning a small scripted joke or a genuine nugget of info. Keep this contained and finite — a handful of hardcoded commands, not an attempt at a real shell.

### 5.5 Robotics.iso

Double-clicking this icon plays a short **mount animation** (a virtual CD spinning, then "mounting" as a drive) before opening a simple file-explorer-style window — the second, and clearest, example of the folder-drill-down approach replacing page-scroll for "digging deeper":

Folders: `First Robot` · `Failures` · `Arduino Era` · `Mars Rover` · `LG Internship` · `Patent` · `Research`

Each folder opens to reveal a small mix of photos, diagrams, short notes, and — where genuinely available — code snippets or short clips. This window is intentionally distinct from the Projects universe: Projects is where finished work gets shown off with interactive case studies; Robotics.iso is the personal, warts-and-included version — including a `Failures` folder on purpose, since a folder of things that didn't work is more human than a highlight reel.

### 5.6 Football.mp4

A CRT-styled media player window (distinct from the PowerDVD music player) containing photos and short notes about football — team experiences, a leadership lesson or two, framed as memories rather than achievements. Ambient crowd/field sound plays softly while this window is focused, then fades when it's unfocused or closed.

### 5.7 Memories.zip

A window styled like an image-preview/archive tool, showing a horizontal carousel of thumbnails with prev/next arrows and a page-dot indicator:

- Suggested set (symbolic rather than necessarily literal): a futuristic cityscape (echoing the wallpaper — the "vision" theme), the Mars rover (robotics), a football stadium (football), a family/group silhouette at sunset (personal/grounding), an underwater/ocean scene (a quieter, reflective image). **Decide explicitly** whether these become real personal photographs or stay stylized/illustrated — both are valid, but it changes the asset production path significantly (photography/curation vs. illustration commissions).
- Clicking a thumbnail expands it briefly within the window (not a full-page lightbox — stay inside the desktop-window metaphor) with a short caption.

---

## 6. Window Manager Mechanics

- **Z-index / focus:** clicking any window (or its taskbar entry) brings it to the front. Active window renders at full opacity with a brighter title-bar highlight; all other open windows dim slightly (reduced opacity, muted title bar) — a real OS convention that also doubles as a subtle visual hierarchy cue when several windows are open at once.
- **Drag:** title-bar drag only (not drag-anywhere-on-the-window), constrained to the visible desktop area (can't be dragged fully behind the taskbar or off-screen). Include a touch of physical feel — slight lag/momentum on release rather than the window stopping dead the instant the cursor stops — implemented as simple spring physics (e.g. `react-spring`, or a small custom easing on release), not a full physics engine. Small detail, but it's the difference between "a window" and "a window that feels like it has weight."
- **Minimize:** window scales down and slides toward its taskbar entry (a simple "genie" approximation via scale+translate, not a literal genie-effect shader), disappearing into a taskbar button that stays present until the window is explicitly closed.
- **Maximize:** optional, but if implemented, should snap the window to fill the desktop area (not true fullscreen/fullscreen browser API) minus the title bar and taskbar — a "reading mode" for windows with more text (My Story.exe benefits most from this).
- **Close:** a quick fade+scale-down, removing both the window and its taskbar entry. Reopening (via desktop icon or a Start-menu-style shortcut) restores it fresh, not necessarily at its last dragged position (session-persisted position is a nice-to-have, not required for v1).
- **Taskbar:** bottom bar, Start button (a glossy orb, XP-style) on the far left, open-window buttons in the middle (label + small icon per open window), system tray (sound toggle, CRT toggle, clock) on the right.
- **Start menu (optional nice-to-have):** clicking the Start orb could reveal a small joke/shortcut menu — e.g. quick links styled as "Programs," reusing the same six-page nav in a different visual form, plus a `Shut Down` option that, when clicked, doesn't do anything destructive — it responds with a small joke: *"You cannot shut down curiosity."* This menu is also the natural home for a **`Reset Desktop`** option (see persistence, below) — an obvious, easy-to-find way to return to the default layout if a returning visitor finds their remembered arrangement confusing rather than charming.

**Persistence (adopted):** window state — position, open/closed/minimized, which My Story.exe sidebar section was selected, which PowerDVD playlist was active — persists across visits via `localStorage`, so a returning visitor finds their desktop roughly as they left it. This is a small engineering cost for a real immersion gain: it's the difference between "a webpage pretending to be a computer" and "your computer." The `Reset Desktop` option above exists specifically so this never becomes a trap — persistence should always feel optional and reversible, never like the site "broke" because it remembered something the visitor didn't expect.

---

## 6a. MSN Messenger Widget

A small, semi-transparent "friends list" widget docked in the bottom-right corner, a Y2K-era touch that adds personality at very low cost:

- Styled like classic MSN Messenger — a short contact list with colored status dots.
- Contacts and statuses are jokes, not real people: `Curiosity — Online`, `Sleep Schedule — Offline`, `New Idea — Typing...`, `Patent Approval — Away`, `Mars Rover — Connected`, `Coffee Supply — Critical` (with the classic animated "..." typing indicator on whichever entry is "typing"). Statuses cycle/rotate on a slow timer rather than sitting static — a small ongoing sign of life in a corner of the screen that's easy to miss the first time and fun to catch later.
- Purely decorative/atmospheric — no click interaction required, though a small hover tooltip with an extra one-line joke per contact is a cheap nice-to-have.

---

## 6b. Notification Toasts

Rare, small system-style toast notifications appear from the taskbar corner every few minutes — not frequent enough to feel like nagging, just enough to occasionally catch the eye:
- `Mission Control Update Available` — clicking opens the `secret.exe` Mission Control teaser directly (this makes it the fourth deliberate path to that same easter egg, alongside `secret.exe` itself, the wallpaper's satellite hover, and Robotics.iso — see §9's note on intentional multi-path discoverability).
- `Research Folder Synced` — opens Robotics.iso's Research folder.
- `New Idea Detected` — opens My Story.exe's Ideas section.
- `Memory Archive Updated` — opens Memories.zip.

Each toast is a real shortcut, not just flavor text — clicking it brings the relevant window to focus (opening it if closed). This turns an otherwise-passive detail into one more small, functional discovery path.

---

## 7. Sound Design

| Interaction | Sound |
|---|---|
| Ambient (page-level) | A warm, glassy synth-pad loop — optimistic, airy, low in the mix |
| Window open | Soft XP-style chime/whoosh |
| Window close | A slightly lower-pitched inverse of the open sound |
| Window drag | A very faint, continuous soft friction/whoosh while actively dragging, stopping on release |
| Icon hover | A subtle blip |
| Terminal keystroke | Soft mechanical key click, one per character typed (both in the boot sequence and any real user typing) |
| PowerDVD play/pause | A physical-feeling click, like a real player button |

All governed by `audioStore` category volumes, independent of global mute — consistent with Home.

---

## 8. Dark Mode

Only the Hero-equivalent (the full desktop view) has been mocked for dark mode so far, and the difference is currently subtle — mostly a darker sky/wallpaper tone with the same window chrome. **This needs more deliberate differentiation before it's build-ready**, and remains the single biggest unresolved design item for this universe. Working name: **AERO → MIDNIGHT OS** (or `LONGHORN-X`, if a more literal OS-era reference reads better).

Where light-mode Aero-OS's emotional register is **dusk / hope / the future** — an open, optimistic desktop — Midnight OS should read as **late-night coding / deep thinking / insomnia / ideas that only show up at 2am**. Same desktop, same windows, same interaction model — but a genuinely different mood, not a recolor: true midnight rather than dusk, cooler and darker glass chrome, neon rather than warm-glow lighting, a quieter and more introspective ambient soundtrack, and (per §3a) a wallpaper that reads as more isolated/still even at its "busiest" state. This is the same order of transformation NEXUS represents for Home — a different emotional address for the same identity, not a lighting toggle.

---

## 9. Easter Eggs

- **system_info.sh** as an interactive mini-terminal (§5.4) is the primary one — and it should also respond to the keyword **`~about`** specifically as a nod to the site-wide "~ opens a terminal" convention from your original brief, in addition to whatever other small command set it supports.
- **Recycle Bin**: opening it reveals a small joke — deleted/rejected project ideas, an old failed-project folder, a self-deprecating note, or an "empty bin" animation with a wink at the camera.
- **`secret.exe`** — a genuinely hidden icon (not in the main column; tucked somewhere a curious visitor might stumble on, e.g. behind another icon, or revealed only after some interaction) that opens a brief **Mission Control teaser** — a flash of the same command-center dashboard that lives as a hidden connection inside the Projects universe's ROS2 dashboard easter egg. This is the first thread of that easter egg, seeded here rather than only in Projects — finding it in About Me should make a visitor want to go looking for its "real" counterpart later.
- A hidden image somewhere in the desktop/Robotics.iso content — the very first robot ever built, presented with no fanfare, just a small caption. A genuine "if you found this, you were paying attention" moment.
- **Inspect-element easter egg** (site-wide, not Aero-OS-specific, but worth noting here since About Me's terminal makes it thematically relevant): a console.log message when the browser DevTools are opened, in-voice ("Since you're already looking under the hood — here's the actual robotic arm's kinematics repo: ..."), rather than a generic "hello world."
- **A note on the Mission Control tease specifically:** by this point it has four independent discovery paths within About Me alone — `secret.exe`, the wallpaper's satellite hover (§3a), Robotics.iso's content, and the notification toast (§6b). This redundancy is deliberate, not accidental — different visitors explore differently (some hover everything, some read every folder, some just wait and watch), and giving the same payoff several honest paths in maximizes the odds that *someone* stumbles onto the thread, without requiring any single visitor to find all four to "get it."

## 9a. Closing Beat

After a reasonable amount of engagement on the page (a time-on-page or interaction-count threshold — e.g. "at least two windows opened" — rather than a scroll position, since this page doesn't scroll), a **`README.txt`** window auto-opens on top of everything else, styled as a real system-status readout rather than plain prose:

```
README.txt

SYSTEM STATUS

Identity:  In Progress
Curiosity: Running
Dreams:    Compiling
Future:    Loading...

No critical errors detected.


NEXT DESTINATION:
PROJECTS
```

The `NEXT DESTINATION` line renders as a glowing, double-clickable shortcut. Double-clicking it initiates the standard transition sequence into the Cuphead-verse (Projects) universe, giving About Me a natural, in-voice way to hand off to the next universe — distinct from (and in addition to) the usual global nav/Navigator route. This window can be closed and dismissed like any other if the visitor isn't ready to move on; it doesn't force navigation, it just offers it.

---

## 10. Mobile Adaptation

Free-floating draggable windows don't translate to touch — the mobile version (confirmed by the existing mobile mockup) restructures the same content into a **vertically stacked, non-draggable card sequence**:

- Same wallpaper scene, now as a tall hero image at the top rather than a full-viewport background windows float over.
- Each "window" becomes a fixed-position card in a scrollable column, in a sensible reading order: My Story.exe → PowerDVD → Things I Like sticky note → Robotics.iso → Football.mp4 → Future.txt → system_info.sh terminal → Memories.zip carousel.
- Windows keep their visual chrome (title bar, traffic-light-style buttons) for aesthetic consistency, but the buttons become inert or map to simpler actions (e.g. "minimize" just collapses that card's content, rather than genuinely floating it to a taskbar, since there's no taskbar-equivalent on mobile — the bottom tab bar serves as primary nav instead).
- Terminal and PowerDVD remain interactive (typing still works; play/pause still works) — only the free dragging/window-management layer is removed, not the content interactivity.

---

## 11. Accessibility Notes Specific to About Me

- **CRT overlay** is off by default whenever `prefers-reduced-motion` or `prefers-contrast` is set, and always has an independent manual toggle regardless (§2) — scanline/bloom effects can be genuinely uncomfortable for some visitors even without a formal motion sensitivity.
- **Draggable windows need a non-drag path**: every window must be fully readable and openable via keyboard/screen reader without requiring drag interaction — dragging is a nice-to-have physicality layer on top of, not a requirement for, accessing content. Tab order should move through desktop icons, then open windows, in a sensible sequence.
- **Terminal input** must be a real, properly labeled `<input>` (`aria-label="About Me terminal — type a command"`), not a div faking an input, so it's usable via screen reader and keyboard alone.
- **Idle character animation** (breathing, head-turns) respects `prefers-reduced-motion` by freezing on a single natural resting frame rather than looping.

---

## 12. Architecture Note (addendum to the locked architecture doc)

This page's window-management system is substantial enough to warrant its own home in the repo, similar to how `editor/` got a dedicated top-level folder for the blog CMS. Recommend adding:

```
os/                          # Aero-OS's desktop/window system — reusable if any other universe ever wants "windowed" content
├── WindowManager.tsx         # z-index ordering, open/close/minimize state
├── DraggableWindow.tsx       # shared window chrome + drag behavior
├── Taskbar.tsx
├── DesktopIcon.tsx
├── StartMenu.tsx
├── CRTOverlay.tsx
├── NotificationToast.tsx     # §6b — rare taskbar toasts, each a shortcut to a window
├── useDesktopPersistence.ts  # §6 — reads/writes window state to localStorage; backs "Reset Desktop"
└── apps/
    ├── MyStoryApp.tsx
    ├── PowerDVDApp.tsx
    ├── TerminalApp.tsx
    ├── RoboticsApp.tsx
    ├── FootballApp.tsx
    ├── FutureTxtApp.tsx
    ├── DownloadsApp.tsx
    └── MemoriesApp.tsx
```

This keeps `scenes/` reserved strictly for R3F/3D content (per the locked architecture's own rule) and gives the OS-window system a clear, separate home rather than awkwardly living inside `components/` or `universes/aero-os/`.
