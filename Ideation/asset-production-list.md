# The Asset Production List — Complete Production Checklist

Reference: every universe spec, the Transition Bible, Audio Bible, and Accessibility Bible

This is the last of the planning documents, and it's a different kind of document from the other ten — not philosophy or rationale, but an actual **checklist of what needs to be produced**: illustrated, modeled, recorded, written, or coded, before or during the build. Where the other documents explain *why* something should exist, this one just lists *that it needs to exist*, organized so an illustrator, sound designer, 3D artist, or writer can look at one section and know exactly what's on their plate. Priority tags (V1/V2) carry over directly from each universe's own spec — nothing is re-prioritized here.

**A scope decision worth stating explicitly:** a full enterprise production-management schema was proposed for this document — per-asset metadata (owner, approval status, hour estimates), a multi-stage approval-gate pipeline, and exhaustive material/texture/shader libraries in the style of a large game studio's asset pipeline. That level of apparatus makes real sense for a multi-department team with a separate approval authority. It's not obviously right here, and I don't have enough information about your actual production setup — solo build, freelance illustrators, AI-assisted art passes, some combination — to know how much of it would help versus just be paperwork standing in for actual production. Rather than guess, this document adds the parts that are valuable regardless of team size (naming conventions, file-format guidance, a proper shader/effect inventory — a real gap in the first draft) and flags the heavier apparatus (per-asset metadata schemas, hour estimates, formal approval gates) as worth revisiting once you know who's actually building this and how.

---

## 0. How to Use This Document

- **V1** = needed before launch. **V2** = needed eventually, not blocking launch.
- Assets are grouped by universe first, then by type within each universe, since that's how the *design* was specced — but §9 re-groups everything by discipline (illustration / 3D / audio / writing) for teams who want to batch work by skill instead.
- "Copy/Content" rows marked **[YOUR INPUT NEEDED]** are things no amount of design work can substitute for — they require your actual voice, your actual data, or a real decision only you can make. These are called out specifically because they're the easiest category to accidentally leave until last.

### 0a. Naming Convention (one consistent pattern, not per-asset documentation)

Cheap to define once, real to enforce — a flat, predictable pattern avoids the actual chaos multi-asset projects run into:

```
{universe}/{feature}/{asset-name}_{variant}.{ext}

Examples:
home/hero/portrait_light.webp
home/hero/portrait_dark.webp
home/hero/portrait_mobile.webp
projects/arm/arm_model.glb
projects/arm/arm_wireframe.png
audio/contact/ambience_loop.ogg
audio/contact/star_chime_01.wav
transitions/effects/glitch_overlay.webm
```

That's the whole convention — universe, then feature, then a descriptive name, then a variant suffix where one exists. No separate ID scheme, no ownership metadata baked into the filename; the folder structure already encodes universe and feature, which is the information that actually matters for finding a file later.

### 0b. File Formats by Asset Type (one reference table, not repeated per asset)

| Asset type | Source format | Export format(s) |
|---|---|---|
| Illustration | PSD / AI / Figma | WebP (primary), AVIF where supported, PNG fallback for transparency |
| Icons/UI | SVG | SVG (inline where possible), PNG fallback |
| 3D models | Blender (.blend) | GLB/GLTF (compressed via Draco/Meshopt per the architecture doc) |
| Audio | WAV (source/master) | OGG (primary), MP3 (fallback) |
| Video/loops | ProRes or similar (source) | WebM (primary), MP4 (fallback) |
| Shaders/effects | GLSL or equivalent | Bundled with the component using them, not a separate export step |

This single table replaces re-stating format requirements on every individual asset line in §1–7 below.

### 0c. Variant Axes (a reference, not a per-asset mandate)

Rather than enumerating every asset against every possible variant (which would recreate the documentation overhead already declined in §0's scope note), one shared reference for which axes exist and which categories typically need them:

| Axis | Typically applies to |
|---|---|
| Desktop / Tablet / Mobile | Nearly everything — layout and illustration crops |
| Light / Dark | Every universe (all six have confirmed or planned dark variants) |
| Reduced Motion | Any animated asset — see each universe's own Motion table (Accessibility Bible §3) |
| High Contrast | Text, UI chrome, achievement/stamp iconography |
| Lite Experience | Heavy illustration/3D assets specifically (Home's face mesh, Projects' room) |
| Print/PDF | Resume only |

Check new assets against this table once, at creation time, rather than retrofitting variants after the fact.

---

## 1. Home (Earth-Prime) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Hero collage art (GARV/ARORA typography + split photo/wireframe portrait) | Illustration | V1 | Both light and NEXUS dark variants |
| Face mesh 3D model | 3D | V1 | Scan-based or sculpted; needs rigging for rotation, material-blend shader, dispersion shader |
| Mars rover "polaroid" photo | Illustration | V1 | |
| Achievement/mission tag card art | Illustration | V1 | Small ID-card-style asset |
| Orbital diagram graphic (Builder Manifesto) | Illustration | V1 | Simple animated ellipse + dot |
| Robotic arm 2D illustration (Builder Manifesto parallax layer) | Illustration | V1 | Distinct from Projects' 3D arm — this is a 2D parallax asset |
| Mini R3F arm scene (`BuilderArmScene`) | 3D | V1 | Or its static-illustration fallback if the flag is off |
| Universe Gateway card art × 5 (About/Projects/Blog/Resume/Contact previews) | Illustration | V1 | |
| Universe Gateway card live micro-loops × 5 | Animation (Lottie/Rive) | V1 | Desktop icons moving, arm servo twitch, ASCII assemble, stamp press, signal pulse |
| Constellation star field + connecting-line graphics | Illustration/Animation | V1 | Primary stars (6) + secondary/hidden stars |
| **Constellation secondary star content** (first robot, first line of code, formative failure, teacher influence, favorite book, meaningful memory) | Copy | V1 | **[YOUR INPUT NEEDED]** — structural placeholders exist, real specifics don't yet |
| Identity In Progress 4-stage animation (rotation/material-blend/dispersion) | Animation | V1 | Scroll- and velocity-linked |
| NEXUS dark-mode hero variant | Illustration | V1 | Confirmed direction, asset not yet produced for bands 2–5 |

---

## 2. About Me (Aero-OS) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Wallpaper scene (rooftop city, day + night) | Illustration | V1 | Seasonal variants V2 |
| Desktop icon set × 9 (My Story, Football, Robotics, Design, Music, Memories, Future.txt, Downloads, Recycle Bin) | Illustration | V1 | |
| Window chrome template (title bar, buttons, Aero-glass treatment) | UI Component | V1 | Reused across every window |
| PowerDVD player skin | UI Component | V1 | Including live waveform visualizer |
| Sticky note + Polaroid photo art | Illustration | V1 | |
| MSN Messenger widget art | UI Component | V1 | |
| Figure idle-loop animation (breathing, per-window reactions, rare stand/walk equivalent) | Animation | V1 | |
| Boot sequence animation (AERO OS v3.14 loading) | Animation | V1 | |
| CRT overlay shader (scanlines, bloom, chromatic aberration) | Effect | V1 | Toggleable |
| Wallpaper state-reactivity (window-count brightness, PowerDVD-synced river pulse, terminal-open satellites, secret.exe building light-up) | Animation | V1 | |
| **Who Am I bio copy** | Copy | V1 | **[YOUR INPUT NEEDED]** — draft direction exists, needs your real voice |
| **Values / Vision / Ideas content** | Copy | V1 | **[YOUR INPUT NEEDED]** — explicitly flagged as needing real input, not placeholder |
| Journey folder content (Childhood/Leadership/Technology/Creativity/Today) | Copy | V1 | Sourced from `content/experience.json` |
| Robotics.iso folder content (First Robot/Failures/Arduino Era/Mars Rover/LG Internship/Patent/Research) | Copy + Illustration | V1 | Photos/diagrams/notes per folder |
| Random.txt joke pool, terminal command responses, MSN status jokes, shutdown joke | Copy | V1 | Small, finite, easy to underestimate as "just copy" |
| README.txt closing text | Copy | V1 | |
| **Memories.zip photo set** | Photography or Illustration | V1 | **[YOUR INPUT NEEDED]** — real photos vs. illustrated is an open decision |
| MIDNIGHT OS dark-mode variant | Illustration | V2 | Currently only the base wallpaper tone is confirmed; needs real design pass |

---

## 3. Projects (Cuphead-verse) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Full room scene illustration (desk, shelves, whiteboard, posters, window, string lights) | Illustration | V1 | Storybook style, per confirmed mockups |
| Robotic arm 3D model + IK rig | 3D | V1 | |
| Rover 3D model + track scene | 3D | V1 | |
| ROS2 dashboard UI (telemetry panels, map view, diagnostics) | UI Component | V1 | Connects to real rosbridge |
| Mission Control hidden room scene | Illustration/UI | V1 | Bonus deeper layer, plus Robotics Timeline Wall content |
| Second terminal / HayaiOS boot sequence UI | UI Component + Animation | V1 | |
| Notebook + AR marker illustration | Illustration | V1 | |
| Research Board illustration (corkboard, patent artifacts) | Illustration | V1 | Includes blueprint-draws-itself animation for patents |
| Electronics Drawer UI | UI Component | V1 | |
| Mirror (star-portal) illustration + OpenCV filter overlays | Illustration + Effect | V1 | ArUco/AprilTag/edge-detection/object-detection/pose-estimation overlays |
| Sketchbook illustration + flip-page content | Illustration + Copy | V1 | Wireframes, sketches, at least one crossed-out page |
| Seated figure idle-loop + rare stand-up-and-walk animation | Animation | V1 | |
| Ambient life system (screen updates, lamp flicker, drafting papers) | Animation | V1 | |
| Day/night + seasonal window variants | Illustration | V1 (day/night) / V2 (seasonal) | |
| Innovation Graph visualization | UI/Animation | V2 | Whiteboard + per-object connection lines, shared data source |
| Workshop Rhythm System sound-tempo logic | Audio/Engineering | V1 | Not literal music — see Audio Bible §3.3 |
| Project info-panel copy × 5 (Arm, Rover, Dashboard, HayaiOS, KinectFusion) | Copy | V1 | Real facts, already drafted per Projects spec |
| Easter egg copy (coffee mug jokes, rubber duck) | Copy | V1 | |

---

## 4. Blog (Brutalist Archive) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Orbit background (void/starfield) | Illustration | V1 | |
| Window chrome template (`POST_XXX.TXT` style) | UI Component | V1 | Per-post-type variants (Essay/Mission Log/Research Note/Build Log/Archive Fragment) |
| ASCII-art generation pipeline (image → dot-matrix, thumbnail + full size) | Tooling | V1 | Editor-integrated, publish-time, not per-visitor |
| Light-mode "recovered lab documents" texture set | Illustration | V1 | Stamps, paper folds, coffee rings, correction marks |
| ASCII-decode transition animation (flatten/pixelate/ASCII-form) | Animation | V1 | Shared with cross-universe entry |
| Post Birth Sequence animation | Animation | V1 | |
| Editor UI (three-pane: insert/canvas/meta) | UI Component | V1 | Tiptap-based, per architecture |
| Reference Block UI (destination-aware label styling) | UI Component | V1 | |
| **Actual blog posts** | Copy | Ongoing | Not a one-time asset — real, continuing content production |
| Ghost Post content × 3–5 | Copy | V2 | Specific, deliberately unresolved fragments |
| Archive Fragment example entries | Copy | V1 | Quick-capture content type |
| Post template starter content × 2 (Essay, Mission Log for V1) | Copy | V1 | |
| Archive Resonance tonal-palette logic | Audio/Engineering | V1 | Not literal music — see Audio Bible §3.4 |

---

## 5. Resume (Dossier) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Paper/parchment texture (light + charred dark variants) | Illustration | V1 | Curled corners, torn edges, coffee stains, tape |
| Technical blueprint portrait | Illustration | V1 | Hero-scale, ~40% viewport |
| Mars rover engineering sketch | Illustration | V1 | |
| Wax seal graphic | Illustration | V1 | Appears at open and close |
| Achievement stamp icon set, tiered sizing | Illustration | V1 | Tier A/B/C sizing per real achievement weight |
| Patent file illustrations × 2 (5-DOF arm, mmWave bot) | Illustration | V1 | Includes miniature technical sketch + reference number |
| Education building sketch | Illustration | V1 | |
| Dossier Metadata box UI | UI Component | V1 | |
| Field note handwritten elements | Illustration | V1 | A few, sparingly placed — visible, not hidden |
| Unfolding entry sequence animation | Animation | V1 | Portal → case → seal-break → unfold |
| End of Dossier closing sequence | Animation | V1 | |
| Dynamic Ink scroll-draw animation | Animation | V1 | SVG stroke-dashoffset, portrait first |
| Stamp-press / download-ritual animation | Animation | V1 | Folder closes → EXPORTED stamp → PDF downloads |
| PDF export template | Tooling | V1 | Genuinely separate clean template, not a screenshot of the themed page |
| **Real resume data** (experience, skills, achievements, education, credentials) | Copy/Data | V1 | **[YOUR INPUT NEEDED]** — much of this exists from your real resume; needs final structuring into `content/*.json` |
| **Values / Vision-adjacent copy, Current Status sheet content** | Copy | V1 | **[YOUR INPUT NEEDED]** |
| Redacted-document joke text, handwritten field notes | Copy | V1 | |
| Recruiter Mode alternate layout template | UI Component | V2 | Same data, plain rendering |

---

## 6. Contact (Vapor-Signal) Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Vaporwave sunset cityscape, 3-layer parallax | Illustration | V1 | Foreground/midground/background |
| Astronaut + cat character art | Illustration | V1 | Idle-loop frames |
| Reaching hand illustration (wireframe/constellation texture) | Illustration | V1 | Particle-flow treatment, not a static mesh |
| Sky/star background + Signal Relay objects | Illustration | V1 (sky) / V2 (relays) | |
| Contact icon set × 4 (GitHub/LinkedIn/Instagram/Email) | Illustration | V1 | Simple icon row per confirmed mockup |
| Connection-sequence entry animation | Animation | V1 | Establishing Connection → Signal Locked → Channel Open |
| Constellation generator (typing → pitched stars → melody) | Animation + Audio | V1 | |
| Submission glitch sequence | Animation | V1 | Reuses shared `glitch` effect |
| Transmission Channel closing footer + Cross-Universe Signal Sources | UI Component | V1 | |
| Form field UI + validation states | UI Component | V1 | Full ARIA flow per Accessibility Bible |
| Lavender-dawn light-mode variant | Illustration | V1 | Already confirmed by mockup |
| Elaborate per-platform contact objects (hologram/terminal/photo-slide/beacon) | Illustration | V2 | Confirmed escalation path, not required for V1 |

---

## 7. Cross-Universe / Shared Assets

| Asset | Type | Priority | Notes |
|---|---|---|---|
| Cursor skins × 6 (Dot/Bubble/Hand/Pixel/Quill/Glitch) | UI Component | V1 | Plus Projects' context-aware glyph overrides (⚙/✎/>) |
| Transition effect library × 12 (glitch, collapse, portal, desktopCrash, archiveRebuild, flatten, pixelate, asciiForm, decay, paperUnfold, paperBurn, signalWarp) | Animation/Engineering | V1 | Each reusable across multiple universe pairings |
| Multiverse Corridor fragment assets | Animation | V1 | Mostly reused from each universe's own particle/residue assets — minimal new work |
| Multiverse Transit Tone | Audio | V1 | One 300–500ms motif, shared across all 30 transitions |
| Universe Leitmotifs × 6 | Audio | V1 | 2–4 notes each |
| Multiverse Navigator UI (floating widget + expanded map) | UI Component | V1 | |
| Command Palette UI | UI Component | V1 | |
| Accessibility Control Center UI | UI Component | V1 | Full settings surface per Accessibility Bible §2 |
| Colorblind filter SVGs × 4 (protanopia/deuteranopia/tritanopia/achromatopsia preview) | Effect | V1 | `feColorMatrix` definitions |
| High-contrast token sets × 6 (one per universe) | Design Tokens | V1 | Targeting 7:1+ contrast |
| Dyslexia-friendly font integration | Typography | V1 | Licensing + implementation |
| Skip-link + landmark markup | Engineering | V1 | Not a visual asset, but a required build item |
| Legacy era mode stylesheets × 4 (1998/2005/2012/plaintext) | Illustration/Engineering | V2 | Per original brief, deferred |

---

## 7a. Shader & Effect Inventory (a real gap in the first draft, now consolidated)

Shaders and GPU-driven effects were scattered across individual universe specs (a "material-blend shader" mentioned for Home, a "dispersion shader" mentioned separately, a CRT overlay mentioned for About Me) without ever being gathered into one list — worth doing here since several of these are shared or near-identical across universes, and a shader written once for Home may need only minor adaptation for a similar effect elsewhere.

| Shader/Effect | Used by | Priority |
|---|---|---|
| Face material-blend (photo texture ↔ wireframe crossfade) | Home | V1 |
| Vertex-dispersion / particle-scatter shader | Home (face mesh), reused conceptually by the Transition Corridor | V1 |
| CRT overlay (scanlines, bloom, chromatic aberration, barrel vignette) | About Me | V1 |
| Colorblind `feColorMatrix` filters × 4 | Site-wide (§7) | V1 |
| Glitch/RGB-split shader | Home, Blog, Contact, shared transition effects | V1 |
| ASCII/dot-matrix conversion (image → character art) | Blog | V1 |
| Portal/vortex distortion | Home, Resume, shared transition effects | V1 |
| Paper-burn edge dissolve | Resume | V1 |
| Signal-warp/interference distortion | Contact, shared transition effects | V1 |

This table is a planning reference, not a new production requirement — every effect listed already exists in its respective universe spec; this just gathers them so a shader/effects engineer can see the full scope in one place rather than discovering them one at a time while reading six separate documents.

## 7b. Shared Texture & Surface Reference

Same consolidation move as §7a, applied to textures — these already appear scattered across individual universe specs; gathering them here means a texture artist sees the full reused set at once rather than discovering overlap piecemeal:

| Texture | Used by |
|---|---|
| Aged/worn paper (light) | Resume (base), About Me (field notes), Home (scrapbook annotations) |
| Charred/burned paper (dark) | Resume (dark mode) |
| Blueprint/technical-drawing paper | Resume (portrait, sketches), Projects (whiteboard, Research Board) |
| CRT noise/scanlines | About Me (CRT overlay) |
| Film grain / halftone dot | General stylistic texture, applied thinly where each universe's spec calls for it |
| Coffee stain, tape, paint spot | Resume, About Me, Home's scrapbook layer |
| Ink splatter / pencil-sketch linework | Projects (stronger linework direction), Resume (technical sketches) |

**Right-sized deliberately** — this is the set actually called for across the eleven documents, not an exhaustive generic texture catalog. Add to it only when a specific universe spec calls for something new, not preemptively.

## 7c. Shared Particle Reference

| Particle type | Used by |
|---|---|
| Twinkling stars | Home (constellation), Contact (sky) |
| Constellation connecting lines | Home, Contact |
| Face-mesh dispersion particles | Home |
| ASCII/character fragments | Blog, shared transition effects |
| Paper/ash fragments | Resume (paperBurn), shared transition effects |
| Signal/light particles | Contact (hand, cursor trail), shared transition effects |
| Dust motes | Projects (ambient room life), general atmosphere where a universe spec calls for it |

Same principle as §7b — reused across universes, worth building once and sharing rather than recreating per-universe.

## 7d. Material Reference (scoped to the two 3D-using universes only)

**Deliberately small, not a full photoreal PBR material library** — given the confirmed illustrated/storybook art direction (not photoreal rendering) and that only Home and Projects use real 3D at all, an exhaustive material catalog (brushed aluminum vs. painted steel vs. brass, etc.) would be solving a rendering problem this project doesn't have:

| Material | Used by |
|---|---|
| Skin/photo-texture (face mesh) | Home |
| Wireframe/glass (face mesh alternate state) | Home |
| Painted metal (robotic arm) | Projects |
| Worn wood (desk, workbench) | Projects |
| Rubber/plastic (rover, small props) | Projects |

Five materials, stylized to match each universe's illustrated look — not a generic reusable library, since these are specific to the two universes that need any 3D material definition at all.

## 7e. Video/Loop Assets

A category worth calling out separately, since several effects already listed in §7a/§7b are often more practically produced as pre-rendered, compressed video loops (WebM, per §0b) than as live shaders — cheaper on the GPU and simpler to build for anything that doesn't need to react to real-time input:

| Loop | Likely production method | Used by |
|---|---|---|
| CRT noise/scanline overlay | Pre-rendered WebM loop | About Me |
| Film grain | Pre-rendered WebM loop | General atmosphere, applied thinly where called for |
| Signal interference texture | Pre-rendered WebM loop | Contact |
| Transition Corridor background noise | Pre-rendered WebM loop | Shared transition effects |

Anything that needs to react to scroll, cursor, or form state (the material-blend shader, dispersion shader, colorblind filters) stays a live shader — this category is specifically for the ambient, non-reactive texture loops where pre-rendering is simpler and cheaper than live computation.

---

## 8. Audio Asset Summary (full detail in the Audio Bible)

| Category | Count/Scope | Priority |
|---|---|---|
| Per-universe ambient tracks | 6 | V1 |
| Per-universe interaction sound libraries | 6 sets | V1 |
| Audio Identity Reconstruction (Home's 4-stage arrival) | 1 sequence | V1 |
| Workshop Rhythm System (Projects) + Archive Resonance (Blog) | 2 systems | V1 |
| Constellation pitched-tone system (Contact) | 1 system | V1 |
| Multiverse Transit Tone + Leitmotifs | 7 short assets | V1 |
| Rare/surprise audio events | ~6, one per universe | V2 |
| Audio Descriptions (narrated) | 6, one per universe's signature experience | V2 |

---

## 9. Regrouped by Discipline (for teams batching by skill)

- **Illustration:** every universe's environment/background art, character art (portrait, figure, astronaut+cat), icon sets, texture work (paper, CRT, vaporwave), universe card previews. The single largest category by volume.
- **3D:** Home's face mesh, Home's optional mini arm scene, Projects' full room + robotic arm + rover + IK rig. Confined to exactly two universes, per the locked "minimal R3F" stack decision.
- **Motion/Animation:** worth breaking down by category rather than one undifferentiated bucket, since a motion designer batches work this way:
  - *Entry/Exit:* each universe's named arrival ritual (boot, unfold, connection, decode, Identity Reconstruction) and exit flourish.
  - *Idle:* every figure's idle loop (About Me, Projects, Contact's astronaut+cat), ambient life systems (screen updates, string lights, wallpaper drift).
  - *Hover/Interaction:* icon/window/stamp hover states, drag physics, per-object click interactions.
  - *Scroll-linked:* Home's Identity In Progress, Resume's Dynamic Ink.
  - *Transition:* all 12 shared effects (§7) plus the Corridor.
  - *Rare/surprise:* easter-egg-triggered animations, one per universe.
  - *Error/Success/Loading:* form validation states (Contact), submission confirmations, asset-failure fallback states (Accessibility Bible §9a).
- **Audio:** §8 above, in full.
- **Writing/Content:** the **[YOUR INPUT NEEDED]** items flagged throughout §1–6 are the highest-priority writing work, since design and code can't substitute for them — bio copy, Values/Vision, real resume data, blog posts (ongoing).
- **Engineering-adjacent assets:** the ASCII-generation pipeline, PDF export template, ROS2 dashboard/rosbridge integration, OpenCV filter integration, colorblind filter SVGs, high-contrast tokens — these sit between "asset" and "feature," worth staffing accordingly rather than assuming they're pure design handoffs.
- **AI-assisted production (a practical note, not a formal category):** given the scale of illustration work across six universes, a meaningful share of it will likely go through an AI-generated concept pass before human refinement — reasonable, and worth planning for explicitly rather than discovering ad hoc: keep the source prompts archived alongside final assets, and retain editable/layered source files (not just flattened exports) from the refinement pass, so a later revision doesn't require regenerating an asset from scratch.

---

## 9a. Optional Detailed Spec Template (for the handful of genuinely complex assets, not all ~150)

Most rows in §1–7 are complete as a single line — a sticky note graphic or a hover sound doesn't need a fifteen-field production sheet. A small number of assets are genuinely complex enough to benefit from a fuller spec before someone starts building them. Rather than mandate this format for everything (which would turn documentation into its own project), it's offered here for the handful that actually warrant it: **Home's face mesh, Projects' IK-rigged robotic arm, the ROS2 dashboard/rosbridge integration, and the OpenCV mirror filter system.** Worked example for one of them:

> **Asset: Home Face Mesh**
> - **What it needs to do:** rotate front→profile→front on scroll; crossfade between photo-texture and wireframe material based on scroll progress; disperse into a particle cloud driven by both scroll position and cursor velocity; reform on band exit.
> - **Source:** a scan or sculpted model, rigged for the above.
> - **Export:** GLB, Draco-compressed.
> - **Dependencies:** the material-blend shader and dispersion shader (§7a), `useFaceMeshScroll.ts` and `useVelocityGlitch.ts` (per the architecture doc), the `enableFaceMesh` feature flag and its static-image fallback.
> - **Variants needed:** light-mode and NEXUS dark-mode material treatments; a reduced-motion fallback (static crossfade sequence, no live shader); a low-end-device fallback (the same static crossfade, gated by the feature flag).
> - **What "done" looks like:** all four Identity In Progress stages read clearly at 60fps on a mid-range laptop, degrade gracefully to the static fallback when the flag is off, and the reduced-motion version conveys the same four-stage narrative without any live shader work.

Use this shape for the other three flagged assets when it's time to build them; don't retroactively apply it to the rest of this document. **The "Dependencies" line in the worked example above is worth calling out specifically** — a lightweight "uses X, used by Y" reference, not a full dependency-graph schema, is genuinely worth carrying into the other three flagged assets too (Projects' IK arm depends on the arm 3D model, the IK library, and the material reference in §7d; the ROS2 dashboard depends on the rosbridge connection and its offline-fallback state; the OpenCV mirror depends on the Web Worker setup and the camera-permission flow). Simple enough to maintain by hand, and it catches the real, common failure mode of changing one piece and not realizing what else quietly depended on it.

**On hour estimates, ownership fields, and formal approval gates specifically:** these are left out deliberately, not overlooked. Estimating hours responsibly requires knowing who's actually doing the work and with what tools (an illustrator working traditionally vs. an AI-assisted pass vs. a freelancer quoting by the piece all have wildly different real timelines) — inventing plausible-sounding numbers without that context would be false planning confidence, not useful information. Ownership and approval-gate fields assume a team structure (multiple contributors, a separate approving stakeholder) that may not reflect how this is actually getting built. Worth adding once that context exists, not before.

---

## 10. Suggested Production Order

Following the build order already established in the Architecture doc, translated into asset terms:

1. **Cross-universe shared assets first** (§7) — cursor skins, transition effects, Navigator UI, Accessibility Control Center. Nothing universe-specific can be properly tested without these existing.
2. **Home's assets** — the first fully-built universe per the locked build order; validates the whole illustration-to-code pipeline.
3. **Projects' assets** — highest technical risk (3D, IK, OpenCV, rosbridge); tackle while there's still time to descope if something proves too costly.
4. **About Me, Blog, Resume, Contact** — lower technical risk once the shared systems and pipeline are proven on Home and Projects.
5. **[YOUR INPUT NEEDED] items** — flag these to yourself now and start them in parallel with everything else, since writing/data-gathering has a different bottleneck (your time and memory) than design or engineering work, and shouldn't be left until the assets around them are already built and waiting on empty content.
