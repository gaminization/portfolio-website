# The Audio Bible — Complete Sound Design Specification

Reference: `lib/audio/manager.ts`, `lib/audio/sound-library.ts`, `stores/audioStore.ts`, each universe's Sound Design section

If the Transition Bible is where the multiverse concept lives visually, this document is where it lives sonically. Sound has been specced piecemeal across all six universe documents already — this is where it gets consolidated into one coherent system, and where the parts that only make sense at the whole-site level (the emotional arc across universes, the category-volume accessibility system, the technical split between playback and synthesis) actually get addressed.

---

## 0. Core Principle: Sound Tells the Same Story Twice

Every universe's visual identity already carries an emotional register — warm, cold, mechanical, cosmic. **Audio should never just accompany that register; it should independently confirm it**, so that a visitor with their eyes closed (or a screen reader user with sound on) could still tell which universe they're in and what's happening. If a sound could be swapped between any two universes without anyone noticing, it isn't doing its job.

---

## 1. The Emotional Arc Across the Journey

The six universes' ambient sound isn't uniform — it deliberately rises, cools, and rises again across the primary loop, avoiding the sonic fatigue of one continuous mood for the entire site:

| Universe | Emotional register | Why here in the arc |
|---|---|---|
| Home (Earth-Prime) | Optimistic, exploratory synth | The opening — establishing curiosity and identity |
| About Me (Aero-OS) | Warm, glassy, nostalgic pad | The most intimate universe — sound should feel like being let into a personal space |
| Projects (Cuphead-verse) | Mechanical, tactile, alive | Grounded and physical — the site's midpoint turns toward craft and process |
| Blog (Brutalist Archive) | Cold, sparse, textual | The most cerebral/detached point in the journey — deliberately the "coolest" register on the site |
| Resume (Dossier) | Physical, restrained, quiet | Warms slightly from Blog's coldness — serious, but human; evidence made tangible |
| Contact (Vapor-Signal) | Expansive, cosmic, spacious | The finale — the widest, most open sound on the site, reaching outward rather than closing in |

**The shape this makes on purpose:** warm → warm → grounded → cold → warm-but-serious → expansive. Blog sitting at the emotional low point (coldest, most detached) between Projects' warmth and Resume's returning warmth is deliberate — it gives the middle of the journey real dynamic contrast rather than everything sounding like a variation on the same pad. This mirrors the visual arc already established across the six specs (each universe's own distinct register) applied specifically to sound's emotional temperature.

---

## 2. Sound Category System (accessibility-driven, per your original brief)

Four independently-adjustable categories, per the Accessibility Control Center already specified site-wide — not a nice-to-have, a real requirement:

- **Music** — the generative/reactive layer (Home's scroll-tied synth, Contact's pitched constellation-melody generator, §3.6) and any melodic ambient content.
- **Ambience** — the steady-state atmospheric loop per universe (workshop hum, CRT static, wax-and-paper room tone).
- **Effects** — discrete, triggered interaction sounds (clicks, hovers, drags, transitions).
- **Voice** — audio descriptions (§8) and any narrated content — kept as its own category specifically so a visitor who wants ambience and effects but not spoken narration (or vice versa) can have exactly that combination.

Each category has its own volume slider, independent of the global mute — a visitor should be able to mute Music entirely while keeping Effects audible for interaction feedback, or any other combination, without an all-or-nothing toggle being the only option.

**A conceptual sub-distinction worth naming, without adding a fifth slider:** within the Ambience category specifically, it's useful to think in two layers — **Ambient Identity** (the tonal/musical pad that defines a universe's emotional register, §1) and **Environmental Life** (discrete-but-continuous world-sounds like wind, distant machinery, electrical hum, paper shuffle in the background). Both are "always on" and both belong under the single Ambience slider — this is a mental model for sound designers building each universe's mix, not a reason to add a fifth accessibility category beyond the four your original brief specified.

---

## 3. Per-Universe Audio Identity (consolidated reference)

Full detail lives in each universe's own spec — this table exists so the whole site's audio can be reviewed as one system rather than six scattered sections.

### 3.1 Home (Earth-Prime)

| Band/Moment | Ambience | Notable Effects |
|---|---|---|
| Hero | Low, calm synth pad | Paper/tape rustle on annotation reveal |
| Identity In Progress | Scroll-and-velocity-reactive synth hum (Tone.js) | Glitch/static burst on rapid cursor movement |
| Builder Manifesto | Ambient continues, slightly warmer | Faint servo tick, timed to the arm's idle micro-movements |
| Achievement Constellation | Ambient quiets, near-silent | Soft chime per star hover; louder tone on click; subtler chime for hidden/secondary stars; **larger/more significant achievements produce a richer chord rather than a single note** — a cheap way to make the constellation's musical feedback actually track the achievement's real weight, consistent with the tiered visual sizing already used for Resume's stamps |
| Universe Gateway | Ambient fades toward the destination universe's mix | Short "whoosh" per card hover |

### 3.2 About Me (Aero-OS)

Warm, glassy Frutiger-Aero synth pad as the base ambience. Window open/close chimes (XP-style, inverse pitch for close), drag friction, icon-hover blips, terminal keystroke clicks (both the boot sequence and live typing), PowerDVD's real playback controlling the actual ambient track. **The mood-shifting playlist system** (2–3 playlists, V1-scoped per the About Me spec) is this universe's most distinctive audio feature — selecting a mood shifts the ambience itself, not just what's "playing" in the PowerDVD window.

### 3.3 Projects (Cuphead-verse)

Workshop hum, fans, faint electricity, distant rain, ambient servo noise — the most "alive-sounding" steady ambience on the site, appropriate for the universe whose stated top priority is feeling inhabited. Arm interactions produce motor whirs and servo clicks; the rover produces motor sounds and sensor pings; the ROS2 dashboard's Mission Control payoff includes radio chatter and telemetry beeps; the second terminal's boot sequence has its own keyboard/fan sounds.

**The Music-category gap, resolved — Workshop Rhythm System, not a conventional soundtrack:** rather than adding literal background music (a "jazz-inspired workshop score" was proposed and explicitly rejected — it conflicts with the same "not a generic startup-website soundtrack" restraint already established for this universe, and adding one would work against the room's grounded, tactile identity). Instead: the room's existing interaction sounds — servo clicks, terminal taps, printer hum, drawer slides — subtly lock to a shared, barely-perceptible tempo rather than firing independently. Nothing about this reads as "music" to a visitor; it just makes the room's machinery feel like it's working in concert rather than as unrelated noises that happen to coexist. This closes the gap without importing a soundtrack this universe was never meant to have.

### 3.4 Blog (Brutalist Archive)

A soft CRT hum layered with faint, distant modem noise and tape hiss — deliberately period-appropriate to the `1999` archive marker, and the coldest ambience on the site by design (§1). Paper-shuffle on window hover (motion also stabilizes), a "decode" sound timed to the ASCII-transition stages when opening a post, mechanical click/shuffle while dragging a window or note, terminal blip on command-palette open.

**The Music-category gap, resolved — Archive Resonance, not a conventional soundtrack:** like Projects, this universe should never get literal background music — it would damage the cold, restrained archive atmosphere §1 establishes deliberately. Instead: exploration actions — opening a post, following a Reference Block, searching, typing a command-palette query — each produce a single note from a small, limited tonal palette. Over a session, a visitor's own exploration gradually assembles an unplanned, sparse ambient composition, built entirely from their actual browsing, not a track playing underneath it. This is information becoming music, not music added to information — a distinct identity that a generic ambient score could never produce, and it fits Blog's archive-of-thoughts framing more precisely than a soundtrack would.

### 3.5 Resume (Dossier)

**Physical sounds only — no sci-fi or futuristic UI sounds anywhere on this page**, a hard rule established in the Resume spec and worth restating here since it's the one universe with an explicit sound *exclusion* list. The wax seal's snap and paper unfolding on entry, a soft folder-opening sound on expanding a section, subtle paper rustle on hovering a stamp or document, a faint sparse pen-scratching sound timed loosely to Dynamic Ink's scroll-drawing, and the stamp sound timed to the `EXPORTED` mark during PDF download.

### 3.6 Contact (Vapor-Signal)

A slow, spacious synth pad — the most overtly "sci-fi" ambience on the site, appropriately, since this is the multiverse's edge rather than a physical space. A very faint, high shimmering tone tied to cursor speed (the signal-trail cursor effect), a short chime on hovering a contact icon, and a rising synth sweep timed to the submission glitch sequence, resolving into a calm tone on confirmation.

**The constellation generator produces real pitched tones, not generic blips:** as each star appears while typing (Contact spec §6), it plays an actual musical note rather than an undifferentiated click — constrained to a pleasant scale (e.g. pentatonic, so any input produces something that sounds intentional rather than arbitrary regardless of what's typed). The result: every message composes its own short, unique melody as it's written, and no two submissions sound quite alike. This is the one place on the site where a visitor's own input directly becomes music, not just triggers it — a fitting, literal payoff for the finale universe.

---

## 4. Idle, Interaction, and Rare Sounds — A Shared Taxonomy

Three tiers, applied consistently across every universe rather than each spec inventing its own categorization:

1. **Ambient/idle** — the continuous base loop per universe (§3), always present at a low level, the "you are here" sound.
2. **Interaction sounds** — discrete, triggered by a specific user action (hover, click, drag, type, submit). These should be the most numerous category and the most consistently implemented, since they're the primary sonic feedback loop for using the site at all.
3. **Rare/surprise sounds** — tied to each universe's own "ambient life" system (About Me's wallpaper state changes, Projects' inhabited-room surprises, Contact's astronaut/cat reaction on submission) or to easter eggs specifically. These follow the same "rare enough to reward attention, not frequent enough to become expected" discipline already applied to every universe's visual easter eggs — a sound that plays every single time isn't a surprise, it's just a longer interaction sound.

**Concrete examples per universe, tied to each universe's existing hidden content rather than invented separately:** a hidden harmony variant on discovering one of Home's secondary constellation stars; a ghostly, half-remembered notification jingle inside About Me's Recycle Bin or `secret.exe`; a brief workshop-radio crackle somewhere in Projects, unconnected to any specific object; a genuinely corrupted-sounding audio fragment inside one of Blog's Ghost Posts (fitting their "unfinished, abandoned" framing exactly); an old recorder-click sound tucked into Resume's redacted-document or UV-light easter eggs; a brief, unidentified distant signal somewhere in Contact's sky. Each is rare enough that most visitors never hear it, and each is tied to content that already exists rather than adding a new hidden-object system just for sound.

---

## 5. Transition Audio (full detail in the Transition Bible, summarized here)

- **Crossfade, never a hard cut:** the `AudioManager` singleton crossfades ambience from origin to destination universe over the course of every transition, timed to the visual effect stages rather than snapping at any single moment.
- **The Multiverse Transit Tone:** one shared, short audio motif (roughly 300–500ms) plays on every single transition — all 30 possible pairings — layered on top of the ambience crossfade. This is the sonic equivalent of a recognizable "you are crossing dimensions" cue, independent of which two universes are involved.
- **Audio residue (extending the Transition Bible's visual Corridor concept):** the same way visual fragments briefly linger into the Corridor rather than vanishing the instant a universe is left, that universe's ambience shouldn't cut off instantly either — a fading echo of the origin's tone briefly overlapping the Transit Tone and the destination's rising ambience, rather than three audio events happening in strict sequence. This is cheap (a slightly longer fade-out on the origin track, timed to overlap rather than a hard stop) and it makes the sonic transition match the visual one's "bleeding between realities" quality rather than feeling like two separate systems that happen to run concurrently. Concretely, per universe: **leaving Home**, the synth pad lingers faintly, as if possibility hasn't fully disappeared; **leaving About Me**, a last, ghostly trace of the OS chime persists; **leaving Projects**, servo clicks and workshop hum fade out gradually rather than cutting; **leaving Blog**, CRT buzz and a trailing scrap of typewriter rhythm linger into static; **leaving Resume**, paper movement and a pen-scratch echo persist briefly; **leaving Contact**, signal noise and a fading constellation tone drift into the Corridor.

---

## 5a. Audio Identity Reconstruction (Home's arrival ritual, matching its visual counterpart)

The Transition Bible gives Home a named visual arrival ritual — Identity Reconstruction — closing a gap where every other universe had one and Home didn't. The same gap existed here, audio-side, until now: a four-stage sequence mirroring the visual staging exactly, playing on arrival at Home regardless of origin universe:

1. **Particles** — tiny, disconnected digital tones, no harmonic relationship to each other yet.
2. **Wireframe** — the tones begin resolving into small, connected intervals, echoing the mesh forming visually.
3. **Half-real** — a warm pad emerges underneath the intervals, still incomplete, still layered with some of the earlier disconnected tones.
4. **Complete** — the full Home ambience (§3.1) arrives, resolved and whole.

This gives Home an arrival soundscape with the same intentionality as About Me's OS Boot, Blog's ASCII Decode, Resume's Dossier Unfold, and Contact's Signal Lock — closing the last remaining audio-side parity gap between the six universes.

---

## 5b. Multiverse Leitmotifs (distinct from and complementary to the Transit Tone)

The Multiverse Transit Tone (§5) is deliberately universal — the same 300–500ms motif on every transition, signaling "a crossing is happening" independent of which two universes are involved. **Leitmotifs are the opposite: a tiny, distinct 2–4 note musical signature per universe**, recurring specifically within that universe's own ambience, its own transitions, and its own easter eggs — the audio equivalent of Spider-Verse's recurring per-character and per-dimension musical themes.

- **Home:** a rising, ascending 3-note phrase — matching the universe's "potential/becoming" identity.
- **About Me:** a soft, nostalgic bell interval.
- **Projects:** a small mechanical rhythmic figure, not a melody — fitting the Workshop Rhythm System's non-musical character (§3.3).
- **Blog:** a terminal-interval motif — two notes, spare, cold.
- **Resume:** an ink-and-paper motif — closer to a sound texture than a melody, consistent with the physical-sounds-only rule (§3.5).
- **Contact:** a signal motif — the same tonal palette used for the constellation generator (§3.6), so the leitmotif and the interactive melody system feel like one coherent musical language, not two unrelated ideas.

**Where they appear:** briefly inside that universe's own transitions (layered under, not replacing, the shared Transit Tone), inside easter eggs (§4), and occasionally inside the ambient loop itself at a very low level. Over repeated visits, a returning visitor should start to unconsciously recognize each universe's motif — cheap to build (each is only 2–4 notes) and disproportionately effective at giving the site genuine musical memory, the same way a returning Spider-Verse viewer recognizes a dimension before its visuals even fully resolve.

---

## 6. Technical Split: Howler.js vs. Tone.js

Per the locked stack — these serve genuinely different purposes, and most sounds on the site are the former, not the latter:

- **Howler.js (the default, most sounds):** straightforward playback — ambient loops, UI interaction sounds, transition effects, category-based volume control, crossfading between fixed audio files. If a sound is "play this file when X happens," it's Howler.
- **Tone.js (the exception, reserved for genuinely reactive/generative audio):** used only where a sound needs to be synthesized or modulated in real time rather than played back from a fixed file — Home's scroll-and-velocity-reactive synth hum (§3.1), Contact's cursor-velocity-tied shimmer and its pitched constellation-tone generation (§3.6). These are the only currently-specced sounds that actually need Tone.js's synthesis capabilities; everything else in this document is standard Howler playback. Worth being disciplined about this split during implementation — reaching for Tone.js by default where Howler would do is unnecessary complexity for a sound that's just "play this file."

---

## 7. Sound Category Matrix (per universe, for review)

A quick cross-check that every universe actually has content in every relevant category, rather than assuming coverage:

| Universe | Music (reactive/generative) | Ambience | Effects | Voice |
|---|---|---|---|---|
| Home | Scroll/velocity-reactive synth (Identity In Progress); Audio Identity Reconstruction on arrival (§5a) | Per-band ambient pads | Extensive (hover, click, star chimes, whoosh) | Optional, §8 |
| About Me | PowerDVD-driven mood playlists | Glassy synth pad | Extensive (windows, icons, terminal, drag) | Optional, §8 |
| Projects | Workshop Rhythm System — tempo-locked interaction sounds, not literal music (§3.3) | Workshop hum/fans/rain/servo | Extensive (arm, rover, dashboard, terminal) | Optional, §8 |
| Blog | Archive Resonance — exploration-driven tonal palette, not literal music (§3.4) | CRT/modem/tape hiss | Extensive (windows, posts, palette) | Optional, §8 |
| Resume | — (deliberately none; physical-only rule) | Restrained paper/wax room tone | Moderate, intentionally sparse | Optional, §8 |
| Contact | Cursor-velocity shimmer; pitched constellation-tone melody generator (§3.6) | Spacious synth pad | Extensive (icons, terminal, submission) | Optional, §8 |

**Both previously-identified gaps are now resolved, deliberately, not by adding conventional soundtracks:** Projects and Blog each needed *something* in the Music column, but a literal background score would have worked against both universes' established restraint. Workshop Rhythm System and Archive Resonance close the gap using each universe's own existing sounds/interactions, consistent with Resume's already-deliberate absence in the same column — three different, considered answers to "does this universe have music," not one default applied everywhere.

---

## 8. Audio Descriptions (accessibility, V2)

Per your original brief's accessibility spec — "Audio Descriptions for major experiences" as part of the Voice category (§2). This is a real, valuable accessibility feature, but it's genuinely a content-production task (writing and recording narration), not a code feature — worth being honest about the scope difference between this and, say, adding alt text (which is closer to free).

- **What it covers:** a narrated description of a universe's core visual experience, for a low-vision or blind visitor using the site with sound on — e.g. a spoken description of Home's Identity In Progress sequence, or Projects' room and its objects.
- **Distinct from and additional to baseline accessibility:** every universe's real semantic HTML, List Views, and alt text (already required in V1 across every spec) already make the site's actual *content* fully accessible without audio description. This feature adds a richer, more experiential layer on top — describing what something *looks like happening*, not just what information it conveys.
- **V2 scope, deliberately:** given the real production overhead (someone needs to write and record narration for each universe's signature experience), this is scoped as a genuine V2 addition — valuable, and worth actually building eventually, not a permanently-deferred nice-to-have, but not something to let block V1 launch either.

---

## 9. Mute & Global Controls

- A persistent, always-visible mute toggle per universe (already established in each spec's "Persistent/Global Elements" section) — sound is never forced, and turning it off should be a single, obvious action from anywhere.
- Category volumes (§2) persist across universe navigation — muting Music in Home shouldn't require re-muting it again after transitioning to About Me.
- `prefers-reduced-motion` does **not** automatically mute audio (motion and sound are different accessibility concerns) — but every universe's own reduced-motion fallback (documented per-spec) should still make sense with sound on, since the two preferences are independent, not linked.

---

## 10. V1 / V2 Scope

**V1 — Launch Critical**
- Every ambient track and interaction sound documented per-universe (§3), the category volume system (§2), the transition crossfade and Multiverse Transit Tone (§5), Audio Identity Reconstruction for Home (§5a — cheap, four short stages, closes a real parity gap), Multiverse Leitmotifs (§5b — cheap, 2–4 notes per universe, disproportionately effective), Howler-based playback for the vast majority of sounds and Tone.js for the specific reactive cases (§6), the Workshop Rhythm System and Archive Resonance resolving the Projects/Blog music gaps (§3.3, §3.4), Contact's upgraded pitched constellation melody (§3.6), and the persistent mute toggle (§9).

**V2 — Recommended, Not Required for Launch**
- Full audio descriptions (§8) — real value, real production overhead, correctly sequenced after the core experience is built and stable.
- The rare audio events catalog (§4) beyond a small initial set — genuinely worth having, but can grow over time rather than needing to ship complete at launch.
