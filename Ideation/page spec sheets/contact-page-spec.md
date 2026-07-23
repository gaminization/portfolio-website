# Contact Page — Vapor-Signal Universe — Complete Spec

Universe ID: `vapor-signal` · Route: `/contact` · Reference: `universes/vapor-signal/*`

The emotional pitch: this is the last stop in the multiverse, and it should feel like it — **a transmission station at the edge of everything**, sending a signal out into space rather than a contact form at the bottom of a website. Where Dossier closed with "the document is still being written," Contact closes the whole site with "every conversation is a chance to build something extraordinary across universes."

**One real constraint that doesn't apply to most other universes:** this page has a genuine functional job — a message sent here needs to actually arrive as a real email. Everything below is in service of that working reliably, not just looking good while it happens.

---

## 0. V1 / V2 Prioritization

**V1 — Launch Critical**
- The three-layer parallax scene (§3), the astronaut and cat (§4), the reaching hand (§5, static/pulse-on-focus version), the Signal Visualization/Constellation system (§6, core version), the Signal Strength system (§6a — cheap, one shared state value, worth including in V1 rather than deferring), the Transmission Terminal form (§7) with real submission handling (§8 — this is a hard requirement, not flavor), the glitch-on-submit sequence (§9, reusing the shared `glitch` effect, now including the astronaut/cat submission reaction), the simple icon-row contact methods (§10, per the confirmed mockup), Cosmic Coordinates (§11), the Transmission Channel closing footer (§17, excluding the undecided last-signal-timestamp question), and both light/dark modes (§13, already confirmed by mockups).

**V2 — Recommended, Not Required for Launch**
- **Elaborate per-platform contact objects** (§10) — your original brief's ambition (LinkedIn as a floating hologram, GitHub as a terminal, Instagram as a VHS tape, Email as a digital mailbox) is a nice escalation of the confirmed simple icon row, not a replacement for it.
- **Real ISS position data** (§3) — requires an external API dependency; moon phase (simple local math) can be V1, but live ISS tracking is a genuine added dependency worth deferring.
- **Rare "space weather" events** (§3) — meteor showers, aurora tints, dimensional distortions — nice atmosphere, not core.
- **Ambient signal glitches** (§3) — rare scanline/color-separation interference, distinct from and smaller than the submission glitch (§9).
- **Signal Relays** (§3) — small background objects that pulse during submission; nice payoff, not core to the page functioning.
- **The connection-sequence entry animation's full staging** (§2) beyond a simplified version.

---

## 1. Visual & Style Direction

Primary register: **vaporwave glitchcore** — magenta/cyan/purple neon gradients, a retro-futuristic sunset grid horizon, CRT-adjacent scanline/glow treatment, Japanese subtext used decoratively (a stylistic nod, not literal bilingual localization — actual i18n is handled separately per the architecture doc's `next-intl` setup). This is the most overtly "screen/digital" of all six universes — where Dossier is paper and About Me is a desktop, Contact is pure signal and light.

**A proposal to replace the vaporwave city with a "Signal Observatory"** (cliff edge, communication tower, antenna arrays, signal dishes) is not being adopted. This conflicts with two things already treated as authoritative throughout this entire process: the actual approved mockup (a real sunset grid highway and palm-tree skyline, not a placeholder), and your original brief, which explicitly asked for "vaporwave glitchcore" — not an antenna array. "The city feels stylistically disconnected from the rest of the site's robotics/space themes" is a fair taste observation, but it doesn't outweigh confirmed art and your own written brief, the same standard applied to every other proposal-vs-mockup conflict across all six universes.

**This stays outside the R3F budget**, consistent with Blog and Resume — the parallax depth, glowing hand, and drifting planets are achieved via layered 2D illustration and CSS/SVG transforms, not a 3D scene. Only Home and Projects use real R3F.

---

## 2. Entry — The Connection Sequence

The mobile mockup's `01 — 02 — 03` indicator suggests a staged arrival rather than an instant load — flagging this as the interpretation being built against, since the mockup doesn't spell out its meaning explicitly:

1. **Establishing Connection** — a brief static/glitch flicker (reusing the shared `glitch` effect already established for Blog's ASCII-decode transition, not a new one-off).
2. **Signal Locked** — the scene resolves into focus, the sunset grid and astronaut becoming visible.
3. **Channel Open** — the Transmission Terminal (§7) and the reaching hand (§5) settle into their final state, ready for interaction.

Respects `prefers-reduced-motion`: collapses to a simple fade directly into the resolved scene, skipping the staged flicker.

---

## 3. The Procedural Sky & Parallax Scene

Three layers, per your original brief, each moving at a different rate to create depth without needing 3D:

- **Foreground:** the astronaut and cat (§4), sitting on a rooftop ledge, closest to the viewer and most responsive to cursor proximity.
- **Midground:** the vaporwave city — the grid-lined highway receding toward the horizon, palm tree silhouettes, the gradient sunset. A small `YOU ARE HERE` marker with `EST. 2025` sits near the road's vanishing point.
- **Background:** the deep sky — stars, faint nebulae, one or two planets, small satellites.

**The sky is a living system, not a static image** — consistent with the "ambient life" pattern already established for About Me's wallpaper (§3a of that spec) and Projects' inhabited-room system:
- Stars twinkle continuously, at a very slow, staggered rate.
- One or two planets/moons drift almost imperceptibly across the background, at different speeds for depth — **not real orbital physics**, just slow parallax drift; worth stating explicitly so nobody builds an actual solar-system simulator for a background decoration.
- **Moon phase (V1):** a small, simple calculation (no external API — moon phase is computable locally from the date) drives which lunar phase renders in the sky, a small "this is real" detail cheap enough for V1.
- **ISS position (V2):** genuinely live ISS tracking would be a nice further detail, but it requires an external API dependency this page doesn't otherwise need — worth deferring rather than adding a live third-party data dependency for a background flourish.
- **Rare "space weather" (V2):** an occasional meteor shower, a faint aurora-like tint, or a brief "dimensional distortion" ripple — infrequent enough to reward a visitor who lingers, not a constant show.
- **Ambient signal glitches, distinct from the submission glitch (V2, adopted):** very rare, very brief interference — a scanline distortion, a momentary text shift, a flash of color separation — roughly once every 20–30 seconds at most. These are "communication interference," not horror-glitch aesthetics, and they're a different tier from the submission glitch (§9): smaller, rarer, purely ambient, never tied to an action. A separate, slightly more noticeable glitch on hovering a contact icon (§10) — a brief distortion that quickly stabilizes — is a cheap addition worth pairing with this.
- **Signal Relays (V2, adopted):** a few small, barely-visible objects scattered far in the background sky — a relay satellite, a communication buoy, a signal beacon — easy to miss on a first visit. Hovering one reveals a small `SIGNAL RELAY ACTIVE` label. Their real payoff comes at submission (§9): all of them briefly pulse in sequence as the message disperses, giving the "signal sent across the multiverse" line a small visible confirmation that it actually traveled somewhere, rather than just disappearing.

---

## 4. The Astronaut & Cat

The seated figure, consistent with the design language already established for About Me's rooftop figure and Projects' seated figure — subtle idle animation, not a static illustration:

- **Idle loop:** slow breathing, an occasional small shift in posture, looking out at the skyline.
- **"The astronaut notices you" (per your original brief):** when the cursor moves near the figure, his head subtly turns toward it — the same restrained "notices you" language used consistently across Home's constellation, About Me's figure, and Projects' figure, now completing the set on the final universe.
- **The cat:** small, independent idle behaviors of its own — an occasional tail flick, an ear twitch, a slow blink — giving the foreground a second, smaller source of life beyond the astronaut, without needing its own complex behavior system.

---

## 5. The Reaching Hand — This Universe's Hero Element

A glowing, wireframe/constellation-textured hand reaching toward the viewer from the right side of the scene — the clearest visual expression of "reaching across the multiverse to make contact." **This is Contact's equivalent of Home's face mesh, Resume's blueprint portrait, and Blog's Orbit view** — the one image a visitor should remember from this universe specifically. Made of stars, particles, and glowing wireframe fragments rather than anything realistic — celestial, not anatomical. **Not a fixed mesh with occasional shed particles, but constant, slow signal flow through it** — particles subtly entering at the fingertips and exiting at the wrist (or the reverse — whichever reads as "energy passing through," direction is a small art decision), like current running through something rather than a static object that occasionally sparkles. Very slow, almost imperceptible at a glance — the point is that it never looks fully still, not that the motion is loud.

- **Static/base state (V1):** present, gently pulsing on a slow ambient loop, with subtle finger movement and small particle shedding — barely-there motion, almost like breathing.
- **Reactive to cursor proximity:** small finger movements and tiny shifts as the cursor nears it — a restrained version of the same "notices you" language used for the astronaut (§4), and for About Me's and Projects' figures before it.
- **Reactive to form engagement (adopted, cheap):** as the visitor begins filling out the Transmission Terminal (§7), the hand's glow subtly intensifies — brightest at the moment of submission, then settling back to its ambient pulse once the confirmation state (§9) resolves. This ties an otherwise-decorative element directly to the page's actual function: the more real the attempt to connect, the brighter the signal.

---

## 6. Signal Visualization & the Live Constellation Generator (one system, not two)

Your original brief describes this from two angles — "the portal visualizes the message as patterns/waveforms/particles/constellations" and, separately, "a live constellation generator where typing makes stars appear and connect." These are the same mechanic described twice, not two systems to build:

- As the visitor types into the Message field, a canvas layer (positioned near the Transmission Terminal, not obscuring it) generates small stars — driven by the actual content being typed (e.g., a new star per word, or per few characters), each new star connecting to the previous ones with a faint line.
- The result is a genuinely unique constellation shaped by that specific message — nobody's submission produces the same pattern, since it's derived from their actual input, not a generic decorative animation playing regardless of what's typed.
- **On submission:** the constellation crystallizes — a brief freeze/glow — accompanied by a small `CONSTELLATION RECORDED` confirmation, giving the visitor something to notice happened as a direct result of what they wrote, distinct from the generic "message sent" confirmation most contact forms give.

---

## 6a. Signal Strength — One Value, Several Reactions (adopted, independently proposed twice)

Two separate reviews independently proposed the same underlying mechanic — worth taking seriously as a real system rather than the looser "hand glows as you type" this spec had before. A single internal value (`signalStrength`, 0–100), driven by real engagement — message length, how many fields are completed, roughly how long the visitor has been engaging with the form — quietly ties together several elements that would otherwise react independently:

- **Low strength (just started typing):** a few stars appear (§6), the hand's glow (§5) rises slightly above its ambient baseline.
- **Rising strength (message growing):** the constellation visibly expands, more stars connect.
- **High strength (a substantial message):** the astronaut looks upward (§4), the cat notices the movement, the background sky (§3) gains a touch of brightness.
- **Maximum, just before submission:** the hand reaches its brightest glow, the constellation is at its fullest — the whole scene reads as "charged," priming the submission moment (§9) rather than that moment arriving out of nowhere.

This is genuinely cheap to build — one shared state value that a handful of already-existing elements each subscribe to, not a new animation system per element. It's the same "ambient life driven by real state" pattern already used for About Me's wallpaper and Projects' inhabited room, applied here to real-time form engagement instead of idle-room state.

---

## 7. The Transmission Terminal

The form itself, styled as a terminal/console rather than a conventional web form:

- **Fields:** Name, Email, Subject, Message — each with a small icon, consistent with the confirmed mockup.
- **`TRANSMIT MESSAGE`** button — the page's primary call to action, styled with the same neon-gradient treatment as the rest of the universe.
- **`ENCRYPTION: AES-256`** readout with a small progress-bar-style visual and lock icon — this is **thematic dressing, not a literal claim about a custom encryption implementation**. The actual transmission security is whatever HTTPS/the hosting platform already provides; this readout shouldn't be read (or built) as promising a custom encryption layer that doesn't exist. Worth being precise about this distinction so the flavor text never becomes a false technical claim.

---

## 8. Backend & Submission Handling (hard requirement, not flavor)

Per the locked stack: a Next.js Server Action receives the form submission and sends it via **Resend** — no custom email server, no separate backend service.

- **Validation:** real client- and server-side validation (valid email format, required fields) — the vaporwave presentation doesn't reduce the need for a form that actually works correctly.
- **Spam prevention:** since this is a public-facing form, include at minimum a honeypot field and basic rate-limiting — a contact form with zero spam mitigation is a real, foreseeable maintenance problem, not a hypothetical one.
- **Error states:** a failed submission needs a real, visible failure state (styled in-theme — e.g. "Signal lost. Try again." — but genuinely communicating that it failed), not a silent failure that leaves a visitor thinking their message sent when it didn't.
- **Confirmation:** on success, the Constellation Recorded moment (§6) plus a clear textual confirmation that the message was actually received.

---

## 9. Submission & The Glitch Sequence

Per your original brief: "when submitted, page glitches, signal sent across universes." This reuses the same shared `glitch` transition effect already established site-wide (Blog's ASCII-decode, Dossier's entry sequence) rather than inventing a new one-off effect for this single moment. A more detailed choreography, adapted to work with this universe's confirmed elements (the hand and the contact icons, §10) rather than an orbiting planet system:

1. On submission, the Transmission Terminal briefly "locks" — a subtle visual cue that input is no longer editable mid-send.
2. The message visibly transforms into particles (the same constellation stars from §6, not a new particle system).
3. Those particles travel across the scene toward the reaching hand (§5) and briefly past the contact icon row (§10) — a small acknowledgment glow on the icons as the particles pass, rather than "planets reacting," since there's no orbit system in V1.
4. The screen-wide glitch/RGB-split flicker (per the original brief) plays as the particles reach the hand.
5. **The hand opens** — its wireframe fingers spreading slightly, as if releasing the signal — then the particles disperse into the void.
6. **The astronaut briefly looks upward, and the cat follows its gaze toward the sky** — a small, quiet reaction to the signal being sent, giving the two figures (§4) a real emotional beat at the page's climactic moment rather than only reacting to idle cursor proximity.
7. **The background Signal Relays (§3) pulse in sequence** as the dispersed particles pass near them — a small visible confirmation that the signal actually traveled through the scene, not just vanished.
8. The scene resolves into a confirmation state — a brief message acknowledging the transmission, styled in-universe (e.g. "Signal sent across the multiverse.") backed by the real confirmation from §8.

Respects `prefers-reduced-motion`: the glitch flicker and particle-travel choreography are replaced with a simple, immediate confirmation state — the message still visibly sends, just without the multi-step animation.

---

## 10. Contact Methods

**A note on a real discrepancy between your original brief and the confirmed mockup:** your brief describes each platform becoming its own diegetic object — LinkedIn as a floating hologram, GitHub as a terminal, Instagram as a VHS tape, Email as a digital mailbox. The actual approved mockup shows a simpler, uniform icon row (GitHub/LinkedIn/Instagram/Email, each with a small caption). Following the site-wide pattern established elsewhere (confirmed mockups take precedence over the original text brief when they conflict): **the simple icon row is V1**, and the more elaborate per-platform object treatment is a genuine V2 escalation worth building once the core page is stable — not a discarded idea, just correctly sequenced behind the simpler, already-approved version.

**A second, competing escalation proposal to flag rather than adopt outright:** a later round proposed reimagining the social links entirely as an orbiting planet system — each platform a planet with its own orbit ring, satellites, hover-expansion, and click-to-launch travel animation. A subsequent proposal wanted planets back specifically as "Communication Satellites." This is a real conflict with the confirmed icon row, and it's also a *second* competing idea for the same "elevate social links into diegetic objects" goal your original brief already proposed once (hologram/terminal/VHS/mailbox). Building both would mean choosing one eventually anyway. There's also a real technical-budget concern: continuous orbital motion with satellites and per-planet interactions is a meaningfully bigger engineering lift than static themed icons, and pursuing it risks pulling this universe back toward needing real 3D rendering on a page explicitly scoped to stay outside the R3F budget (§1). **This is now resolved, not just flagged:** a separate, independent review rejected orbital social objects for exactly these reasons (added motion complexity, harder accessibility, harder mobile adaptation, higher implementation cost, distraction from the actual goal of sending a message) — two independent reviews landing on opposite sides of this question, with the rejecting position matching the reasoning already established here. **Recommendation, held:** keep the confirmed non-orbiting icon row for V1; if V2 escalation happens, it takes the form of the original brief's per-platform themed objects — refined slightly to `GitHub` as a small terminal card, `LinkedIn` as a holographic panel, `Instagram` as a retro photo slide, `Email` as a transmission beacon — static/themed, not orbiting.

Each icon links out to the real destination (GitHub, LinkedIn, Instagram, `mailto:`) — simple, but worth stating plainly since it's easy to let a themed icon quietly stop being a real, working link during development.

---

## 11. Cosmic Coordinates

Per your original brief: instead of a literal "Bhopal, India," the page displays a stylized version — `Earth Prime / Sector 23.2599° N / 77.4126° E`. **This is your own location, styled, not the visitor's** — no geolocation API involved, just real coordinates dressed in the multiverse's language. Cheap, static, and worth being explicit that this isn't meant to be dynamic per-visitor content.

---

## 12. Cursor System

`GlitchCursor` — the default cursor for this universe, consistent with the architecture's cursor-skin assignments. **Signal trails, specific to this page only:** the cursor leaves a brief trail of cyan/magenta/purple light as it moves — per your original brief, "as if the visitor is drawing signals through space." This effect is exclusive to Contact; no other universe's cursor leaves a persistent trail, keeping it a distinctive, memorable detail for the site's final page rather than a generic effect reused everywhere.

---

## 13. Dark / Light Mode

Already confirmed by existing mockups, not a proposal: dark mode is the neon-night vaporwave scene described throughout this spec; light mode shifts to a **lavender dawn transmission** — the same astronaut, cat, hand, and terminal, but the sunset gradient shifts toward soft pastel dawn tones and the neon accents cool into pastel cyan/lavender. Same scene, same layout, genuinely different time-of-day and mood — consistent with the "own personality per mode" principle established site-wide.

**A later proposal suggested a fuller reimagining** — a "Cloud City Observatory" concept with planets becoming floating balloons and the hand becoming a light sculpture. This conflicts with real approved mockup evidence (the lavender-dawn variant already exists and shows a cooler/pastel version of the *same* scene, not a structurally different one) — holding the confirmed version as authoritative here, the same way confirmed mockups have taken precedence over unconfirmed proposals elsewhere in this spec (§10).

---

## 14. Easter Eggs

- **A `signal` command-palette keyword:** typing `signal` into the site-wide command palette while on this page reveals a small hidden transmission log — consistent with the same site-wide "type something to unlock a bit more" pattern already established (`~about` on About Me and Blog, `clearance` on Resume). This universe's variant of a pattern used consistently everywhere else, not a new mechanic invented just for Contact.
- **"Message in the Stars":** a rare, brief star alignment in the background sky spells out `HELLO` for a few seconds before dissolving back into the normal starfield — cheap, charming, and consistent with the "ambient life rewards lingering" principle used across every universe's background system.
- **A secret hidden object (adapted from a "secret planet" proposal):** since there's no orbit/planet system in V1 (§10), this becomes a small hidden icon or object elsewhere in the scene rather than an unlabeled planet — clicking it reveals something modest and self-aware: *"Still building. Still learning. Still exploring."*
- **A note on a cross-universe companion idea, deliberately not adopted here:** a proposal suggested a small sprite/creature that follows the visitor across all six universes as a hidden easter egg. This is a genuinely interesting idea, but if it's ever built, it belongs in the shared shell layer (per the architecture doc), the same way "Archive Memory" was flagged as a cross-cutting concern rather than a Blog-specific feature — a persistent companion has to be part of `UniverseShell`, not something Contact alone could implement on its own.

---

## 15. Data & Update Workflow

Consistent with Resume's approach (§11 of the Resume spec) and for the same reason: this page's content — email address, social links, any small copy changes — updates rarely enough that a dedicated CMS would be solving a problem this page doesn't have.

- Social links and contact details live in a small structured file, `content/contact.json` (email, LinkedIn, GitHub, Instagram, and optionally X/Discord/Calendly if those are ever added).
- **Updating means editing this file directly and redeploying** — no admin UI, no database. If a link changes or a new platform gets added, it's a one-line edit, not a content-management workflow.
- This keeps Contact consistent with Resume as the two "low-maintenance-by-design" universes, versus Blog's genuinely content-heavy, frequently-updated CMS.

---

## 16. Sound Design

| Source | Sound |
|---|---|
| Ambient (page-level) | A slow, spacious synth pad — portal/transmission ambience, the most overtly "sci-fi" soundscape of any universe (appropriate here, unlike the more restrained physical-sound rule on Resume) |
| Cursor movement (signal trail) | A very faint, high, shimmering tone, barely audible, tied to cursor speed |
| Typing in the Message field | Soft, small blips as each new constellation star appears (§6) |
| Hovering a contact icon | A short chime |
| Submission | A rising synth sweep timed to the glitch sequence (§9), resolving into a calm tone on confirmation |

Governed by `audioStore` category volumes, consistent with every other universe.

---

## 17. Closing — Transmission Channel Footer

Resume closes with "End of Dossier" (§3a of the Resume spec); Contact — as the final universe on the site — deserves matching closure rather than simply ending after the social icons. A quiet footer, styled consistently with the terminal aesthetic:

```
TRANSMISSION CHANNEL

STATUS: OPEN

EVERY CONVERSATION STARTS WITH A SIGNAL


Thanks for visiting.
See you somewhere else in the multiverse.
```

**One piece of this, deliberately left as an open decision rather than adopted outright:** a proposed `LAST SIGNAL: [timestamp of most recent successful submission]` line. This needs real thought before building, not just adopting because it sounds nice — publicly displaying exactly when you last received a message has a real optics tradeoff (a long gap between timestamps could read as low engagement, even if that's not actually true), and it's a small privacy consideration around exposing real visitor-submission activity. Recommend either leaving this out, using a static/non-dynamic line instead (e.g. tied to the last deploy date rather than actual contact activity), or deciding deliberately that a real timestamp is worth the tradeoff — this shouldn't ship by default without that conversation happening first.

### 17a. Cross-Universe Signal Sources

A nice closing callback only this, the final page, can do — a small status-style list referencing all five other universes by their in-world names, each briefly pulsing on hover and linking back:

```
SIGNAL SOURCES

EARTH-PRIME ........... ACTIVE
AERO-OS ................ ACTIVE
THE WORKSHOP ........... ACTIVE
BRUTALIST_ARCHIVE ...... ACTIVE
DOSSIER ................ ACTIVE
```

A quiet reminder, right at the end, that all six universes are still connected — reinforcing the site's core multiverse premise one last time before the visitor leaves, without needing any new mechanism (just links and the universe names already established).

---

## 18. Mobile Adaptation

The three-layer parallax scene compresses into a single scrollable column: hero scene (astronaut/cat/skyline) → Transmission Terminal card → contact methods → Transmission Channel footer (§17) → footer. The `01 — 02 — 03` connection-sequence indicator (§2) persists as a small progress marker during the entry animation. Touch targets (form fields, contact icons) meet the 44×44px minimum established site-wide.

---

## 19. What Not To Build / Accessibility Guardrails

- **Do not let `ENCRYPTION: AES-256` (§7) become an actual false technical claim** — it's thematic flavor text describing a real but unglamorous HTTPS/platform-level security posture, not a custom cryptographic feature to build or to imply exists.
- **Do not skip spam prevention (§8)** on the grounds that the form is "just for a portfolio" — a public form with zero mitigation is a real, predictable maintenance burden.
- **The Signal Visualization (§6) is decorative, never a required step** — a screen reader user or a `prefers-reduced-motion` visitor must be able to fill out and submit the form with zero dependency on the constellation canvas rendering at all.
- **Real form accessibility, non-negotiable:** every field has a real, properly associated label (not just an icon or placeholder text), real error messaging, and full keyboard operability — the vaporwave styling is a skin over a genuinely accessible form, not a replacement for one.
- **`prefers-reduced-motion`** disables the ambient sky drift, the signal trail cursor effect, and the glitch sequence on submit (replaced by the simplified versions described in §2, §9) — the page's actual function (sending a message) is unaffected either way.

---

## 20. Performance Notes

- This universe stays **outside the R3F budget** — the parallax scene, glowing hand, and drifting sky are 2D/CSS/SVG, not WebGL, consistent with Blog and Resume.
- The constellation canvas (§6) should be lightweight (simple 2D canvas drawing, not a particle-heavy engine) since it runs continuously while the visitor types.
- No live external API calls in V1 (ISS position deferred to V2, §0) — keeps this page's load fast and dependency-free at launch.
