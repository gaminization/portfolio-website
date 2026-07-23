# Projects Page — Cuphead-verse ("The Workshop") Universe — Complete Spec

Universe ID: `cuphead-verse` · Route: `/projects` · Reference: `universes/cuphead-verse/*`

This is your highest-technical-risk universe (real R3F, inverse kinematics, a live camera feed through OpenCV, a live ROS2 telemetry dashboard) and, per your brief, the one that most needs to feel like **an innovator's room** — not a project gallery with a 3D skin, but a lived-in workshop you're looking into, where every object on the desk and floor is a real thing Garv actually built. The emotional pitch: *this is where the work happens, and it's still happening right now, whether you're watching or not.*

**Art style: locked as shown in the existing mockups.** Every Projects mockup produced so far is a warm, painterly, storybook-illustration style (soft linework, natural lighting, Ghibli-adjacent) — this is the confirmed final direction, not Cuphead's 1930s cel-shaded cartoon style, despite the universe's internal ID. The internal ID stays `cuphead-verse` for continuity with the rest of the codebase; only the visual brief was ever in question, and it's now resolved. (A later proposal reintroduced literal Cuphead styling — ink outlines, paint-texture wobble, Studio MDHR concept art references. Keeping the locked mockup-based direction unless you deliberately want to revisit it; flagging so this stays a conscious choice.)

---

## 0. V1 / V2 Prioritization

Projects is, by a wide margin, the most technically expensive universe on the site — real R3F, IK, a live camera worker, and a real rosbridge connection, before any of the connective/narrative systems below are even counted. Read this section before building anything else in this document; it determines what actually needs to exist for launch versus what's genuinely additive.

**V1 — Launch Critical**
- **Environment:** the diorama room, ambient camera drift, the seated figure, workshop ambience, and the basic day/night window differentiator (§3, §4, §10's core distinction only).
- **Core objects (all nine):** Robotic Arm (§5.1), Rover Track (§5.2), ROS2 Dashboard (§5.3), Second Terminal/HayaiOS (§5.4), Notebook + AR Markers (§5.5), Research Board (§5.6), Electronics Drawer (§5.7), Mirror (§5.8), **and the Sketchbook (§5.9)** — cheap to build (an illustrated flip-book, no complex interaction) and the room's only non-robotics object, carrying real identity weight that's not worth losing to a scope cut.
- **Interaction framework:** zoom transitions, the context-aware cursor, the Accessible List View, and per-object info panels.
- **Mission Control:** both the always-visible tablet dashboard *and* the hidden room + Timeline Wall — deferring the hidden room to V2 would leave the four About Me easter-egg threads (`secret.exe`, wallpaper hover, Robotics.iso, notification toast) pointing at nothing at launch, which isn't acceptable given that content ships with About Me regardless.

**V2 — Recommended, Not Required for Launch**
- **The Innovation Graph** (§6a) — the unified whiteboard-and-connection-network system. Genuinely the strongest available mechanic for this universe, and worth building — just not before the room itself exists and works. Object Exploration (the room, the zooms, the Mission Control discovery) is already a complete, unique V1 signature mechanic on its own; the Innovation Graph is the *next* one, not a launch blocker.
- **Workshop Progression** (§6b) — the time-based room-activity escalation.
- **Dynamic Exterior World, extended** — seasonal variants (snow, Diwali lights, Halloween details) and the golden-hour day/night transition nuance (§10). The *basic* day/night window swap stays V1; the richer seasonal/weather layer on top of it is V2.

Treat this split as a hard rule while building, not a suggestion — the room reaching V1 complete and shipped is worth more than the room reaching 90% of an all-features-included version and staying unshipped.

---

## 1. Persistent / Global Elements

| Element | Description | Behavior |
|---|---|---|
| **Top nav** | Standard six-item nav, re-skinned in the workshop's warm wood/paper tones | Consistent shared-shell nav per universe theming |
| **Multiverse Navigator** | Reads `05 / ∞` in this universe, styled like a small brass/wood instrument rather than a glowing HUD element — fits the tactile, physical-object language of the room | Auto-minimizes while the visitor is inside a zoomed-in object interaction (arm, dashboard, mirror), reappears at the room's default view |
| **Cursor** | `HandCursor` default (a simple pointing hand, matching the "hand-crafted" register) | **Context-aware** — see §9 |
| **Sound toggle** | Standard placement, styled as a small analog dial rather than a digital icon | Controls this universe's ambient workshop soundscape |

---

## 2. The Room — Camera & Navigation Model

This is the most important structural decision on the page, since it determines what "3D room" actually means in practice:

- **Default view:** a fixed, diorama-style camera angle — the same single, wide view established across every mockup — not a free-fly first-person space. The room is composed like a stage set, viewed from one deliberate vantage point.
- **Ambient camera drift:** a very slight parallax/orbit tied to cursor position (the room subtly "looks toward" wherever the cursor is, within a tight angular limit) — gives a sense of depth and presence without letting the visitor get lost navigating a full 3D space.
- **On mobile:** the equivalent is an explicit "drag to look around" gesture (confirmed by the mobile mockup) — a constrained horizontal/vertical orbit around that same fixed vantage point, not free 6-DOF movement. A short one-time tooltip introduces this on first load.
- **Object interaction (click/tap):** clicking a highlighted object triggers a **zoom transition** — the camera moves in and reframes on that object specifically, replacing the wide room view with a focused interaction view (the arm's IK rig up close; the dashboard's full-screen telemetry view; the mirror's camera-filter view). This directly matches your original brief's "zooms into the mirror" language, generalized to every object.
- **Exit:** a clear, consistent "back to room" affordance (not just relying on browser back) returns the camera to the default diorama view from any zoomed-in state.
- **Accessible alternative:** a full **List View** (per the locked architecture's `AccessibleListView.tsx`) presents every interactive object as a plain, semantic list — name, description, and an "open" action — for screen readers or anyone who doesn't want to navigate a spatial scene. This is a parallel access path, not an afterthought bolted onto the 3D scene.

---

## 3. The Room — Environment & Set Dressing

Everything below is atmosphere and world-building — not directly interactive, but essential to "lived-in" rather than "staged":

- **Desk:** the room's center of gravity — two monitors (one running a code editor, one running a live 3D rover simulation), a desk lamp, a mug (branded, personal), scattered breadboards/wires, a small toolkit.
- **String lights:** strung across the ceiling, warm and soft — twinkle very subtly and continuously, the room's main ambient light-life detail.
- **Bookshelf:** visible spines — *Linux Basics*, *ROS2 by Example*, *C++ Programming*, *Algorithms Unlocked* — real, specific titles rather than generic "book" props. Hovering a spine could reveal a tiny one-line note (a favorite quote or a "still haven't finished this one" joke) — optional, low-priority polish.
- **Whiteboard:** a `sense → think → act → plan` diagram — a real systems-thinking sketch, not filler text. Hover animates the arrows and brightens the labels. **Click elevates this from decoration to a real interaction:** the board zooms into a small systems map (Computer Vision → Localization → Path Planning → Control → Execution; Sensor Fusion → Decision Making → Autonomy; Embedded Systems → Firmware → Hardware → Robotics) — clicking a node is one of the two entry points into the room's Connection Network (§6a). This reframes the whiteboard from "what I've built" to "how I think," which is a stronger, more distinctive use of the object than pure set dressing.
- **Wall posters:** the robotic-arm blueprint poster (ties directly to the Resume universe's engineering-sketch visual language — same motif, different universe), plus superhero and dinosaur posters and a couple of plush dinosaur toys on the shelves — the deliberate "art student, not just tech student" personality touches from your original brief. These should feel genuinely personal, not staged-quirky.
- **Sticky note:** *"Small steps, big impact."* — taped near the desk, consistent with the physical-object/scrapbook language already established on Home and About Me.
- **Window:** looks out on a sunflower field in daylight (light mode) or a night sky with visible moon/stars (dark mode) — this is the universe's primary light/dark differentiator, and it should also be the natural hook for seasonal variants (see §11).
- **Ceiling:** suspended decoration — small aircraft/rover parts, floating sketches pinned at odd angles — reinforcing "things are still being figured out here" even in the space above everything else.

### 3a. Spatial Zones (organizing structure for asset planning)

Although the room reads as one continuous diorama (not physically separated rooms), it's useful to think of it as loosely zoned for asset-production and layout purposes:

- **Left — Research area:** the whiteboard, blueprint poster, and the new Research/Patent Board (§5.6).
- **Center — Workbench:** the robotic arm and the desk's tools/electronics — the room's visual and thematic center of gravity.
- **Right — Testing zone:** the rover track.
- **Rear — Computer station:** the two desk monitors plus the second terminal (§5.4).
- **Corner — The mirror.**

This is a layout aid, not a navigation model — the visitor still experiences one fixed diorama view (§2), not distinct rooms to walk between.

---

## 4. The Seated Figure

Garv sits in a rolling chair at the desk, working — the Projects-universe equivalent of Home's face-mesh centerpiece and About Me's rooftop figure. Same design language: subtle idle animation, not a static illustration.

- **Idle loop:** small, continuous motion — adjusting something on the desk, a slight lean toward the monitor, an occasional glance at whichever object the cursor is nearest.
- **Working animation:** when the room is in its default (non-zoomed) state, the figure periodically appears to actively work on the robotic arm on the desk — a believable "he's mid-project" beat, not a constant animation.
- **Rare animation (low-frequency, genuine surprise):** every few minutes, the figure rolls back from the desk, stands, walks a few steps to the Research Board (§5.6), appears to adjust or study something, then returns to the chair and resumes work. Infrequent enough to reward a visitor who's stuck around, never frequent enough to feel like a scripted loop.
- **Reactive to object state:** the figure occasionally glances toward whichever object the visitor is actively engaged with — toward the rover track during a mid-"mission" run (§5.2), toward the tablet when the ROS2 dashboard is actively showing telemetry (§5.3), or toward the desk itself when the arm's IK interaction is open (§5.1) — the same "window-awareness" pattern established for About Me's figure, applied consistently here.
- **Respects `prefers-reduced-motion`:** freezes on a natural working pose rather than looping.

---

## 5. Interactive Objects (mapped to real projects — confirmed)

Each object in the room represents one specific, real project from your background — mapping confirmed:

| Object | Real project | Why this mapping |
|---|---|---|
| Robotic arm on desk | **Gesture-Controlled 5-DOF Robotic Arm** (patent co-invention) | The arm mockup already exists and is the room's clearest interactive centerpiece |
| Floor rover track (cones/flags) | **mmWave Earthquake Survivor Detection Robot** (patent filed) | The track becomes an obstacle/rubble course the rover navigates — a direct, literal fit for a survivor-detection demo |
| ROS2 Dashboard tablet | **SEDS Rover Team — ROS2 navigation stack** (Nav2, RTAB-Map, real-time elevation mapping) | The mockup's exact telemetry fields (battery %, CPU, nodes, live map) match this project's real deliverables |
| A second terminal/monitor | **HayaiOS — bare-metal RTOS** | Gives your original brief's "computer with a boot sequence and fake terminal" easter egg a real project to showcase |
| Notebook + scattered AR/fiducial markers | **3D Reconstruction — IMU-Enhanced KinectFusion** | The markers are literally fiducial tracking markers — a direct visual match, not just decorative flavor |
| Research/patent board (left wall) | Connective-tissue stories + both real patents | Smaller stories and artifacts that don't need their own full interaction, plus a dedicated framed treatment for the two patents |
| Electronics drawer (workbench) | Cross-project hardware inventory | Not one project — a connective detail showing real hardware reused across multiple real projects |
| Sketchbook (desk corner) | Not project-specific — evidence of the design/creative identity | The room's only non-robotics object; the site elsewhere (Home) establishes "digital artist, creative technologist" as part of your identity, and nothing else in this room shows it |
| The mirror (the star-filled oval portal seen in every mockup) | Not project-specific — a standalone personal/creative interactive | **Confirmed:** the star-filled oval *is* the mirror, not a separate universe-map object — its constellation motif is a deliberate visual flourish (a "mirror to other realities" framing) layered over its actual function, which is the OpenCV camera-filter interaction from your original brief |

### 5.1 Robotic Arm — Gesture-Controlled 5-DOF Robotic Arm

- **Ambient/idle:** occasional small autonomous joint movements even when not being directly interacted with — reinforces "the world feels inhabited," per your original brief.
- **Scroll-reactive (in default room view):** subtle joint movement tied to scroll position, giving it a bit of life even before the visitor clicks in.
- **On click — zoom + interact:** camera zooms to a close, well-lit view of the arm. The visitor can drag individual joints; a lightweight FABRIK-based inverse-kinematics solver (`three-ik` or hand-rolled, per the architecture doc) updates the rest of the chain in real time. Joint transitions animate smoothly, deliberately styled like an Apple-product reveal (your original brief's exact reference) — clean, confident easing, not jerky robotics-sim motion.
- **Content panel:** alongside the live IK interaction, a small info panel states the real project (gesture classifier accuracy, latency, patent status) — the interactive moment and the resume-grade facts sit side by side, not one replacing the other.

### 5.2 Rover Track — mmWave Earthquake Survivor Detection Robot

- **Default state:** a small rover sits at the track's start line; cones and flags mark the course.
- **On click — "Start Mission":** the rover autonomously drives the track (a pre-authored path, not a real physics sim — this is a portfolio demo, not a robotics simulator), pausing briefly at marked points as if "detecting" something, with a small on-screen readout echoing the real project's actual language (confidence-grid mapping, explore/confirm/log state machine).
- **Ambient/idle:** headlights or status LEDs flicker occasionally even at rest — another "inhabited" detail from your original brief.
- **Content panel:** real project facts (mmWave radar through 30cm of rubble, <15cm coordinate error, probabilistic confidence-grid algorithm) surface alongside the demo.

### 5.3 ROS2 Dashboard Tablet — SEDS Rover Navigation Stack (and the Mission Control payoff)

- **Default state:** sits on the floor, screen dimmed/idle, showing a small ambient version of its UI.
- **On click — zoom to full dashboard:** this is the payoff for the four discovery threads seeded across About Me (`secret.exe`, the wallpaper's satellite hover, Robotics.iso, the notification toast) — **this is where "Mission Control" actually lives.** The zoomed view expands into a genuine NASA-mission-room-style dashboard:
  - Live rover telemetry — battery %, CPU load, active node count.
  - A live map view showing the rover's path/position.
  - Live "satellite feed" panels and robot diagnostics readouts.
  - Per your decision: **this connects to a real ROS2 instance** via `rosbridge_suite` + `roslibjs` (Option B from the architecture doc), not simulated data. This is a genuinely bigger commitment than the simulated version, worth being explicit about:
    - **A real robot or ROS2 simulation needs to be running and network-reachable** whenever this dashboard is live — the dashboard's content is only as "live" as that system actually is. Decide and build for what happens when it isn't (see fallback below), rather than treating "always on" as guaranteed.
    - **Security scope must be read-only from the public site.** This dashboard should subscribe to telemetry topics only — it should never expose the ability for an anonymous visitor to publish commands to a real robot. If any control capability is wanted later, it needs its own deliberate auth/safety layer, entirely separate from what a portfolio visitor can reach.
    - **`rosbridge`'s websocket endpoint needs to be genuinely hardened** for public exposure — this is different from a typical web API, since it's a bridge into a robotics system; don't expose it with default/permissive configuration.
    - **Fallback/offline state, required, not optional:** when the real ROS2 instance isn't reachable (robot powered off, network down, between demos), the dashboard should show a clear, in-theme "offline" state (e.g. "Mission Control — no active telemetry" with a last-known-data timestamp) rather than a blank screen or a broken connection error. This is the single most important thing to get right before shipping this feature, since a portfolio visitor seeing a visibly broken dashboard is worse than a simulated one that always looks polished.
  - **Recommendation:** even having chosen the real bridge, build the fallback/offline visual state *first*, alongside a toggle-able local demo-data mode for development and for moments the real system is intentionally offline — so the dashboard degrades gracefully rather than only ever being demoed live.
- **Content panel:** real project facts (Nav2 + RTAB-Map visual SLAM, real-time 3D elevation mapping, 70% reduction in manual teleop time) alongside the live/offline telemetry view.
- **Bonus deeper layer — the hidden Mission Control room:** in addition to the always-visible tablet above (which every visitor can find and which carries the actual telemetry), a small hidden door elsewhere in the room — marked with a tongue-in-cheek `AUTHORIZED PERSONNEL ONLY` sign — leads to a secret NASA-style sub-room, discoverable via the four About Me threads (`secret.exe`, wallpaper hover, Robotics.iso, notification toast) or exploration within this room itself. This room contains *additional* content the tablet doesn't:
  - Extra satellite-feed panels and more detailed diagnostics.
  - A **Robotics Timeline Wall** — a specific, concrete piece of exclusive content: `First Robot → Mars Rover → Research → Patent → Future`, presented like a mission-history plaque rather than a resume timeline. This gives hidden-room visitors something they genuinely can't get anywhere else on the site, rather than a vaguer "more of the same telemetry."
  
  Genuinely rewarding the people who went looking, without ever being the only way to see real telemetry. **This ordering matters:** the tablet is the guaranteed path (every visitor sees the real dashboard); the hidden room is the bonus (only the curious see the extended version).

### 5.4 Second Terminal — HayaiOS Bare-Metal RTOS

- **Default state:** a second monitor/terminal, screen off or showing a screensaver-like idle pattern.
- **On click:** plays a genuine **boot sequence** (per your original brief's "Linux Project... boot sequence starts, fake terminal appears") — a short, satisfying startup log referencing real project details (preemptive scheduler, 1ms tick, context switching, HAL) before landing on an interactive-feeling (but scripted/finite) terminal prompt, similar in spirit to About Me's `system_info.sh` but with its own distinct command set relevant to this specific project.
- **Content panel:** real facts — ARM Cortex-M4, <50 assembly-instruction context switch, mutex/semaphore primitives.

### 5.5 Notebook + AR Markers — 3D Reconstruction (IMU-Enhanced KinectFusion)

- **Default state:** an open notebook with rover-design sketches, several small fiducial/AR markers scattered nearby on the floor — genuinely matching the real project's tracking-marker imagery, not generic desk clutter.
- **On click:** flips through a few notebook pages (sketches, brief handwritten notes) and/or shows a short before/after visual of the depth-reconstruction work — doesn't need a live interactive demo the way the arm or rover do; this project is better served as a well-presented case study than a simulated interaction.
- **Content panel:** real facts — RealSense D455 + IMU complementary-filter fusion, ~50% reduction in tracking failures across a 6-DOF fast-motion trajectory.

### 5.6 Research Board (new)

- **Location:** the left-wall research area (§3a). A corkboard-style board with photos, sticky notes, small handwritten equations, and simple patent sketches pinned to it — this is where the site's "achievements as discoveries" language (already established via Home's constellation) gets a physical, workshop-appropriate form.
- **Hover:** notes/photos enlarge slightly; any connecting strings between related items brighten.
- **Click:** opens a short project-story overlay related to whichever note was clicked — not a duplicate of the dedicated project objects above, but smaller connective-tissue stories (a competition memory, a mentor's advice, a moment of doubt) that don't need their own full interaction.
- **Patent items specifically:** the two real patents (5-DOF Robotic Arm, mmWave Survivor Detection Robot) get a small dedicated framed-artifact treatment within this board — clicking one plays a satisfying "blueprint draws itself" animation (lines appearing progressively, as if being drafted in real time) before settling into the patent's real details.

### 5.7 Electronics Drawer (new)

- **Location:** built into the workbench itself.
- **Click:** slides open to reveal an inventory view — the actual components used across your projects (ESP32, Arduino, sensors, motors) as small labeled items.
- **Interaction:** clicking a component highlights which of the room's projects use it (e.g. clicking "ESP32" highlights both the robotic arm and the rover track) — a small, genuinely useful cross-reference that reinforces these are real, connected projects sharing real hardware, not disconnected demos.

### 5.8 The Mirror

- **Visual:** the star-filled, constellation-patterned oval seen in every mockup — not a plain reflective surface, but a mirror rendered with the same cosmic/multiverse visual language used elsewhere on the site (Home's achievement stars, the general constellation motif). It should read as *a mirror to other realities* even though its actual function, on interaction, is the OpenCV camera-filter feature.
- **Not tied to a specific resume project** — this is a personal, playful interactive, distinct from the case-study objects above.
- **On click:** zooms into the mirror, then explicitly requests camera permission (never auto-starts `getUserMedia` — always a deliberate visitor-initiated action). Once granted, applies real, specific computer-vision modes via OpenCV.js (run in a Web Worker, per the architecture doc, keeping this off the main thread so it doesn't jank the rest of the room):
  - **ArUco / AprilTag marker detection**
  - **Edge detection**
  - **Object detection** (a lightweight in-browser model, in the spirit of your real YOLOv8 work — full YOLOv8 is too heavy to run live in-browser, so this is a genuine but scaled-down demonstration of the same category of technique, framed honestly as such rather than overclaiming real-time YOLOv8 performance)
  - **Pose estimation**

  Each mode overlays real-feeling technical annotations (bounding boxes, confidence scores, coordinate markers, class labels) and includes a short **description / real-world application / lesson-learned** note, so each filter reads as a small showcase of a real technique rather than a decorative effect — e.g. pose estimation's note could reference the gesture-controlled arm's real classifier work. This elevates the mirror from "cool toy" to a small, playful **computer vision lab**, while staying fun rather than turning into a sixth dense case study.
- **Framing:** treat this less as "here's a project" and more as "here's a small, fun thing I built because I could" — a change of pace after several fact-dense case studies. Its constellation visual keeps it feeling like it belongs in this multiverse rather than being a bolted-on webcam widget. A subtle surface ripple on hover, before any click, is a nice cheap tell that the mirror is more than decoration.

### 5.9 Sketchbook (new)

- **Location:** desk corner, partially open, a pencil resting on the open page.
- **Purpose:** the room's only object that isn't robotics/engineering-flavored — every other object says "engineer, roboticist, researcher." Your site elsewhere (Home's "digital artist, creative technologist" framing) establishes a design/creative identity too, and nothing in this room currently shows it. This is deliberately distinct from the Notebook (§5.5), which is scoped specifically to the KinectFusion project's technical sketches — the Sketchbook is general creative evidence, not tied to one project.
- **Content:** a mix of website wireframes, interface ideas, rougher robot sketches, a couple of visibly abandoned/crossed-out concepts, and a small moodboard fragment.
- **Interaction:** clicking flips through a few pages — deliberately including at least one unfinished or crossed-out page. This is one of the most human objects in the room precisely because it shows unfinished, imperfect thinking, not polished output.

---

## 6. Ambient "Inhabited World" System

**This is the room's core priority, not a nice-to-have polish pass.** Every other spec in this document — the projects, the interactions, the mapping — matters less than whether the room genuinely feels like someone is living and working in it right now, whether or not a visitor is watching. Treat "does it feel alive" as a real acceptance criterion when reviewing the built room, not just a mood descriptor.

A single lightweight state layer (not several unrelated ad-hoc animations) governs the room's background life, so it reads as one coherent living space:

- Monitor screens periodically "update" — scrolling code, a shifting simulation view — on a slow, randomized timer.
- The robotic arm makes small unprompted joint adjustments.
- The rover's status lights flicker occasionally.
- String lights have a very slow, continuous twinkle.
- Plush toys/plants have a barely-perceptible idle sway, distinct from their cursor-reactive sway (§7) — a constant low-level ambient motion versus a responsive one.
- The lamp's light very subtly flickers/breathes rather than staying perfectly static.
- Papers/sketches on the desk shift very slightly, as if in a faint draft from the window.
- The mirror's star-field has its own slow twinkle, independent of and continuing even when no one is interacting with it.
- Every object with an idle/ambient behavior should occasionally, rarely, do something slightly unexpected — a book shifting on the shelf, the rover's headlights blinking twice instead of once — small enough to reward a visitor who's been sitting with the scene for a while, without ever feeling scripted or attention-seeking.

This mirrors the same design pattern established for About Me's state-reactive wallpaper (§3a there) and Home's living background elements — keeping "ambient life" a consistent, expected quality of every universe, not something unique to this one.

---

## 6a. The Innovation Graph (V2 — this universe's next signature mechanic, not a launch requirement)

Every universe has one defining mechanic — Home has identity transformation, About Me has desktop exploration, and Projects' V1 mechanic is Object Exploration (the room, the zooms, the Mission Control discovery) — already complete and unique on its own. The Innovation Graph is the *next* layer, worth building deliberately in V2 rather than folded into launch: **everything in this room is connected, and the room can show it.**

**One shared system, two interfaces — not two separate features:** rather than building the whiteboard's systems-map and a general "click any object to see its connections" feature as two independent systems (which would mean maintaining a "whiteboard graph" and a "project connection graph" that inevitably drift apart from each other), both are views onto the same underlying data structure — a single **Innovation Graph** (`content/projects-graph.json` or similar) where every concept and object is a node: Robotic Arm, ESP32, Patent, Computer Vision, ROS2, SLAM, Sensor Fusion, Research, Embedded Systems, Navigation, HayaiOS, Mars Rover, Earthquake Bot, and so on. For example:

- **Robotic Arm** connects to: Patent · ESP32 · Computer Vision · Control Systems.
- **Rover Track** connects to: ROS2 · Navigation · SLAM · Mission Control.
- **Second Terminal (HayaiOS)** connects to: Embedded Systems · Schedulers · ARM · Firmware.
- **Notebook (KinectFusion)** connects to: Perception · Vision · Mapping · Sensor Fusion.

**Interface 1 — the whiteboard's systems map (§3):** visualizes the graph from a systems-thinking perspective — clicking a node in the diagram highlights every room object tagged with that concept.
**Interface 2 — clicking any object in the room:** visualizes the same graph from a project perspective — thin glowing lines briefly connect it to whatever else it relates to, inventor-board style.

Over the course of exploring, the room gradually reveals itself as a network of connected ideas rather than a set of isolated demos — which is precisely the "inventors think in systems, not projects" framing that makes this distinctly an *innovator's* room. This is exactly the kind of thing worth having on the roadmap and wrong to rush — better to ship the room without it and add it once V1 is stable, than to delay launch chasing it.

## 6b. Workshop Progression (V2 — recommended, not required for launch)

A nice extension of §6's ambient-life principle: the longer a visitor stays, the more active the room becomes — an additional monitor waking up, the arm performing a longer autonomous sequence, a notification appearing, faint radio chatter beginning. The core idea is good and worth keeping on the roadmap, with one adjustment: **compress the timescale significantly.** The original pacing (2/5/8/10/15 minutes) assumes dwell times most portfolio visitors won't actually have — most of that content would simply never be seen. Recommend something closer to 30 seconds / 90 seconds / 3 minutes, so a normally-attentive visitor exploring the room actually experiences some progression, not just the most patient few.

**Scope note:** given how much this room already carries in V1 alone — nine interactive objects, a real rosbridge connection, and a full ambient life system — this is additive polish on top of an already-complete room, not something the room is incomplete without. Build it once V1 has shipped and proven stable.

---

## 7. Environmental Reactions to Cursor

Per your original brief, direct and immediate — the room visibly responds to the visitor's presence:

- Plants sway gently as the cursor passes near them.
- The sticky note flutters slightly.
- Light through the window shifts subtly as the cursor moves (a soft suggestion of changing light angle, not a literal time-of-day system).
- The seated figure glances toward the cursor when it's near (§4).

---

## 8. Cursor System (context-aware)

| Hovering | Cursor becomes |
|---|---|
| Default (empty room space) | `HandCursor` — simple pointing hand |
| Robotic arm / rover / any robot-like object | `⚙` gear glyph |
| Notebook / Research Board / Sketchbook | `✎` pencil glyph |
| Monitors / terminal | `>` prompt glyph |
| Electronics drawer | A small component/chip glyph |
| Mirror | A small camera-shutter icon |

Implemented as small SVG/glyph swaps on the existing cursor layer, not entirely separate cursor components — consistent with the shared cursor system's architecture.

---

## 9. Sound Design

| Source | Sound |
|---|---|
| Ambient (room-level) | Soft mechanical hum, a faint breeze through the window, occasional distant birdsong (daytime) or quiet night ambience (dark mode) |
| Robotic arm interaction | Soft servo whirs as joints move |
| Rover "Start Mission" | Small motor start-up sound, soft beeps at "detection" points |
| ROS2 Dashboard open | A satisfying "systems online" chime, then a low mission-control hum |
| Terminal boot sequence | Classic terminal boot audio — soft beeps, a fan-spin-up sound |
| Mirror open | Camera shutter click |
| Object hover | A soft, material-appropriate blip (different for metal/robot objects vs. paper/notebook) |

All governed by `audioStore` category volumes, consistent with every other universe.

---

## 9a. Easter Eggs

- **The Mission Control hidden room** (§5.3) is the headline one — the payoff for all four About Me threads, plus discoverable through exploration within this room itself.
- **Coffee mug:** clicking it repeatedly cycles through a short series of small jokes/messages — a cheap, low-effort detail with real charm, in the same spirit as About Me's `Random.txt`.
- **A small hidden Linux joke** somewhere in the room (a rubber duck, or similar) for anyone who recognizes rubber-duck debugging — a tiny detail aimed squarely at the audience who'd appreciate it, not explained for anyone who doesn't.
- Keep this list intentionally short rather than adding every possible easter egg (a "secret blueprint," "developer notes," "failed projects folder" were all considered) — a few well-placed, well-made surprises land better than many thin ones, and the room already carries meaningful hidden depth via Mission Control and the Research Board's patent reveals.

---

## 10. Dark Mode / Day–Night and Seasonal Variants

- **V1 — confirmed light/dark differentiator:** the window — sunflower field and warm daylight in light mode; night sky, visible moon and stars in dark mode. Interior lighting (string lights, lamp, monitor glow) should shift warmer/cooler to match, rather than only the window view changing. This basic swap is required for launch — it's the universe's primary light/dark mechanism.
- **V2 — richer treatment:** a golden-hour transition tone between day and night (rather than an instant cut), plus full seasonal variants baked into the same window — snow in winter, diyas or string-light color shifts for Diwali, a small pumpkin/cobweb detail for Halloween, etc. (per your original brief, and the natural workshop equivalent of About Me's living wallpaper, §3a there). None of this blocks launch — the V1 day/night swap alone is a complete, working differentiator on its own.

---

## 11. Mobile Adaptation

Confirmed by the existing mobile mockup:

- "Drag to look around" replaces cursor-driven ambient parallax — an explicit, taught gesture (brief onboarding tooltip on first visit) rather than assuming visitors will discover it.
- Tapping a highlighted object surfaces a **bottom card** rather than a full zoom-transition — showing the project title, a short description, relevant tech tags, a "View Project" button, and a bookmark icon. This is a lighter-weight mobile equivalent of the desktop's full zoom-in interaction, appropriate for smaller screens and touch.
- Pagination dots suggest the room's several objects are browsable as a swipeable sequence on mobile, in addition to being tap-discoverable directly in the scene — both paths should work.
- All touch targets meet the 44×44px minimum per your original accessibility requirement.

**Note on an alternative mobile concept:** a "vertical dollhouse, swipe between zones (Workbench/Research/Testing/Mission Control)" pattern was also proposed. This isn't what your actual mobile mockup shows — it depicts drag-to-look-around plus bottom cards, which is what's specified above. The zone-swipe idea isn't wrong, but it would need its own mockups before being treated as confirmed; recommend building against the pattern you already have real reference for, and treating zone-swipe as a future alternative to evaluate only if the confirmed pattern proves insufficient in practice.

---

## 12. Accessibility Notes Specific to Projects

- **List View** (§2) is the primary accessible path into this universe's content — every object's project info must be fully readable there without needing to touch the 3D scene at all.
- **`prefers-reduced-motion`:** disables ambient camera drift/parallax, the arm's autonomous idle movement, and the rover's "Start Mission" autoplay (replaced with a static end-state view plus the same info panel) — content stays fully accessible, just without the live-motion presentation layer.
- **OpenCV mirror:** camera access is always an explicit, clearly-labeled user action — never implied or automatic — and a clear way to stop the camera feed must be equally obvious.
- **Colorblind modes:** the mirror's constellation visual language should be checked against protanopia/deuteranopia/tritanopia filters specifically, since it relies on small bright points against a dark background — a pattern that can be genuinely hard to parse under some colorblind conditions if contrast isn't deliberate.

---

## 13. Performance & Rendering Notes

- This is one of only two universes using real R3F (alongside Home) — budget GPU/render cost accordingly; the diorama-camera model (§2) helps here, since it avoids needing to stream/render a full explorable environment in every direction.
- **IK:** `three-ik` or a small hand-rolled FABRIK solver — lightweight, not a full physics engine.
- **OpenCV:** runs in a Web Worker (`OpenCV.js` WASM build), keeping camera-filter processing off the main thread.
- **ROS2 telemetry:** a real `rosbridge_suite` + `roslibjs` websocket connection to an actual ROS2 instance, per your decision — read-only telemetry subscription only, hardened for public exposure, with a required graceful offline/fallback state when the real system isn't reachable (see §5.3). This is real infrastructure to stand up and maintain, not a one-time asset to build and forget — plan for it accordingly, separately from the rest of the (purely static/client-side) site.
- **Asset compression:** Draco/Meshopt for room geometry, consistent with the site-wide asset pipeline.
- **Network-aware loading:** on a detected slow connection, load the static diorama view first and defer the heavier interactive assets (arm rig, dashboard UI, mirror/OpenCV bundle) until requested.

---

## 14. Architecture Note (addendum to the locked architecture doc)

The existing `scenes/projects/*` structure already anticipated most of this; recommend these additions to match the objects/mapping above:

```
scenes/projects/
├── ProjectsRoomScene.tsx
├── objects/
│   ├── Mirror.tsx              # §5.8 — OpenCV interactive; visual = the star-filled portal
│   ├── RoboticArm.tsx          # §5.1 — IK-driven
│   ├── RoverTrack.tsx          # §5.2 — replaces/clarifies the earlier generic "Car.tsx"
│   ├── Tablet.tsx              # §5.3 — ROS2 dashboard + Mission Control payoff, real rosbridge
│   ├── MissionControlRoom.tsx  # §5.3 — hidden bonus room, separate from the always-visible Tablet (new)
│   ├── SecondTerminal.tsx      # §5.4 — HayaiOS boot sequence (new)
│   ├── Notebook.tsx            # §5.5 — 3D Reconstruction case study (new)
│   ├── ResearchBoard.tsx       # §5.6 — patent artifacts + connective-tissue stories (new)
│   ├── ElectronicsDrawer.tsx   # §5.7 — cross-project hardware inventory (new)
│   └── Sketchbook.tsx          # §5.9 — design/creative identity evidence (new)
├── useInverseKinematics.ts
├── useRosbridge.ts             # §5.3 — real rosbridge_suite/roslibjs connection + offline fallback state (new) — V1
├── useAmbientLifeSystem.ts     # §6 — single shared "inhabited world" state layer (new) — V1
├── useConnectionNetwork.ts     # §6a — Innovation Graph: shared graph powering both the whiteboard map and per-object connection lines (new) — V2
├── useWorkshopProgression.ts   # §6b — time-based room activity escalation (new) — V2
└── AccessibleListView.tsx
```

```
content/
└── projects-graph.json         # §6a — Innovation Graph data: object ↔ concept ↔ object relationships (new) — V2
```

`Car.tsx` from the original architecture is effectively renamed/repurposed to `RoverTrack.tsx` given the project-mapping decided in §5 — flagging this rename explicitly rather than silently diverging from the earlier locked file list.
