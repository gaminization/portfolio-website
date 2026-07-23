# The Accessibility Bible — Complete Accessibility Specification

Reference: `components/accessibility/*`, `stores/accessibilityStore.ts`, `lib/network-tier.ts`, each universe's Accessibility Notes section

Your original brief set real, specific targets for this site — WCAG AA, Lighthouse Accessibility > 95, Lighthouse Performance > 90 — not aspirational language. This document is where those targets stop being scattered across six universe specs and the Transition/Audio Bibles, and become one complete, checkable system. Nothing in here is new invention; it's consolidation of what's already been specced everywhere else, plus the handful of things that only make sense described once, at the whole-site level.

---

## 0. Core Principle: Accessibility Is a Parallel Path, Not a Fallback

Every universe in this project has an elaborate, spatial, sometimes-3D primary experience — and every universe also has a fully-realized, independently accessible path through the same content. The relationship between the two isn't "accessible version = stripped-down version of the real thing" — it's two genuine ways to experience the same real content, built alongside each other from the start.

**A cleaner way to state the same rule — three layers, only one of which is ever optional:**

1. **Content** — the actual information (biography, project details, achievements, article text). Never optional, never removed.
2. **Interaction** — the ability to navigate, filter, search, submit, and reach that content. Never optional, never removed.
3. **Presentation** — the visual/spatial/animated *way* content and interaction are dressed up (face mesh, 3D room, floating windows, paper texture). **This is the only layer that's ever allowed to change or disappear** under any accessibility setting.

Concretely: if Home's face mesh fails to render, the biography and navigation are still fully there. If Projects' 3D room isn't used, the project details, filtering, and links still work through List View. If Blog's Orbit view isn't rendered, articles are still readable, searchable, and navigable through List View. Accessibility removes presentation. It never removes function or content.

- Projects' **List View** isn't a downgrade from the 3D room — it's the room's actual content, described completely, navigable without ever touching a 3D scene.
- Blog's **List View** is built to the same completeness as Orbit, not a stripped-down accessibility afterthought.
- Resume's real semantic HTML is what a recruiter skimming quickly *and* a screen reader user both actually read — the paper aesthetic is a skin over it, never a replacement for it.

**The test for every feature in this document:** if a visitor never encounters the spatial/animated/decorative layer at all — no motion, no sound, no mouse — do they get the complete, real content anyway? If the answer is ever "no," that's a defect to fix, not an acceptable tradeoff for creative ambition.

---

## 1. Hard Targets (restated as requirements, not aspirations)

- **WCAG 2.2 AA** compliance, site-wide (upgraded from the original brief's unversioned "WCAG AA" target — 2.2 is the current standard as of this writing; AAA is a worthwhile aspiration where practical, e.g. contrast ratios, but AA is the hard requirement).
- **Lighthouse Accessibility score > 95**, checked in CI (Lighthouse CI, per the locked architecture), not just spot-checked manually before launch.
- **Lighthouse Performance score > 90**, since performance and accessibility are linked for visitors on low-end devices or slow connections (§8, §9) — a beautiful but slow site is not accessible to everyone regardless of its markup.
- **Progressive enhancement architecture** — the site should degrade gracefully at every layer (no JS, slow connection, reduced motion, screen reader, low-end GPU), never catastrophically. A visitor with three of those five constraints at once should still get a working, readable site, not a broken one.
- **Reflow at 320 CSS pixels width / 400% browser zoom** (WCAG 1.4.10, a genuine gap in the earlier draft): the site must remain fully usable at this scale — no horizontal scrolling required, navigation stays operable, forms stay usable, content stays readable. This is distinct from the user-controlled Text Size setting (§2) — browser-level zoom affects the entire layout, not just text — and needs its own explicit testing pass, not an assumption that text scaling alone covers it.

---

## 2. The Accessibility Control Center (the master settings surface)

A single, always-visible control panel (per the original brief), containing every user-adjustable accessibility setting in one place — not scattered across per-universe menus. Full setting list:

| Setting | Options | What it actually changes |
|---|---|---|
| **Text Size** | 80% / 100% / 125% / 150% / 175% / 200% | Real CSS scaling of actual text, not just a cosmetic zoom — layouts must reflow correctly at every step, tested specifically at 200% across mobile, tablet, desktop, and ultrawide, since that's where layouts are most likely to break. |
| **Font Choice** | Default / Dyslexia-Friendly | Swaps body text to a dyslexia-friendly typeface (e.g. Atkinson Hyperlegible, Lexend, or OpenDyslexic) — applies globally, including inside each universe's themed typography, without breaking any universe's specific display-font branding (headlines can stay thematic; body copy respects this setting everywhere). Pairs with increased line-height/paragraph spacing and reduced justification when enabled. |
| **Contrast** | Normal / High Contrast | A real high-contrast theme variant per universe, not a single global filter slapped over everything — each universe's own token system (`resolveTheme`) should support a high-contrast token set (targeting 7:1+ contrast ratios), consistent with how light/dark and seasonal variants already work. |
| **Motion** | Full Motion / Reduced Motion / No Motion | See §3 — three tiers, not a binary, since "reduced" and "none" are genuinely different needs. |
| **Audio** | On / Off | The global mute, independent of the four category volumes already established in the Audio Bible (§2 there) — this is the single top-level switch; the category sliders live in their own settings area, referenced from here. |
| **Cursor Effects** | Enabled / Disabled | Disables custom cursor skins, trails (Contact's signal trail), and velocity-based effects (Home's glitch-on-fast-movement) — falls back to the system default cursor. Independent from Motion, since someone might want reduced motion elsewhere but still find a custom cursor fine, or vice versa. |
| **Cursor Size & Contrast** | Size: Default / Large / Extra Large; Contrast: Normal / High Contrast | A distinct dimension from Cursor Effects above — this controls the *size and visibility* of whichever cursor is active (system or custom), important for low vision, tremor, or older visitors, independent of whether custom cursor skins are enabled at all. |
| **Colorblind Modes** | Off / Protanopia / Deuteranopia / Tritanopia, each with **Correction** and **Preview** sub-modes | Correction applies an `feColorMatrix` filter (per the locked architecture) to help a colorblind visitor see better. Preview does the reverse — lets *any* visitor simulate what a given condition looks like, primarily useful for you, reviewers, or recruiters checking the site's own accessibility, and as a nice side effect of making accessibility visible rather than invisible infrastructure. Achromatopsia (total color blindness) is worth including in Preview specifically, though a true "correction" filter for it is less meaningful than for the three color-deficiency types — flagging that distinction rather than implying all four modes work identically in both directions. |
| **Cognitive Load Reduction** (V2) | Off / On | A distinct axis from Motion — see §3a. Hides decorative side panels, simplifies layouts, disables ambient-life systems, and removes non-essential easter-egg indicators, independent of whether motion itself is reduced. |
| **Low-Vision Mode** (V2) | Off / On | **Distinct from Cognitive Load Reduction above, not a duplicate of it** — where Cognitive Load Reduction targets *complexity and stimulation* (fewer decorations, less simultaneous motion), Low-Vision Mode targets *legibility and clarity* specifically: increased line and paragraph spacing beyond the Font Choice setting, reduced transparency on glass/blur effects (relevant especially to About Me's Aero-glass windows and Contact's transparent terminal panels, both of which can reduce text contrast against a busy background), and stronger, higher-contrast focus indicators beyond the baseline "focus rings are never removed" rule (§2a). The two settings can be enabled independently or together — a visitor might want one, the other, both, or neither. |

This panel is reachable from every universe identically (part of the shared shell, not reinvented per universe), and every setting persists across navigation — adjusting Text Size in Home shouldn't reset when arriving at About Me.

### 2a. Keyboard Shortcuts & Navigation Matrix

A canonical reference, consolidating what was previously scattered across multiple sections — this is the interaction contract every universe implements identically, not something each universe reinvents:

| Key | Action |
|---|---|
| `Tab` | Move to next focusable element |
| `Shift+Tab` | Move to previous focusable element |
| `Enter` | Activate a link or primary action |
| `Space` | Activate a button/toggle |
| `Escape` | Close the active modal, window, or panel |
| `Arrow Keys` | Navigate within grouped content (e.g. a list of options, a set of tabs) |
| `Home` / `End` | Jump to the start/end of a list or document |
| `Cmd/Ctrl+K` | Open the command palette |
| `Shift+A` | Open the Accessibility Control Center |

- **A redundancy worth flagging rather than adopting wholesale:** a `G` + letter chord scheme (`G H` for Home, `G A` for About, etc.) was proposed for fast universe navigation. This duplicates something already locked — the site already has a command palette (`⌘K`) that serves exactly this purpose, reachable from every universe. Adding a second, parallel keyboard-shortcut system for the same underlying task means visitors would need to learn two different ways to do one thing, which works against usability rather than for it.
- **Visible focus rings are mandatory, never removed** — a common but harmful pattern (`outline: none` without a replacement) that should be explicitly disallowed in code review, not just assumed to be avoided by default.

---

## 3. Motion — Three Tiers, Not a Binary

`prefers-reduced-motion` (system-level) and the manual Motion setting above (§2) both feed the same underlying state, consolidated here since it's currently the most-repeated rule across every universe spec — worth stating once, completely, rather than trusting six separate restatements to stay in sync:

- **Full Motion (default):** everything as specced per-universe — scroll-scrub animations, ambient drift, cursor-velocity reactivity, full transition choreography.
- **Reduced Motion:** the tier most universes already default to under `prefers-reduced-motion` — scroll-scrubbed animations become simple crossfades, ambient drift/parallax stops, cursor-velocity effects disable, transitions collapse to simple cross-fades (per the Transition Bible's global rule), but all content remains fully present and correctly ordered.
- **No Motion:** a stricter tier beyond what `prefers-reduced-motion` alone typically triggers — even the small idle "ambient life" touches (About Me's wallpaper drift, Projects' inhabited-room surprises, Home's twinkling stars) stop entirely. Everything static, nothing moving anywhere on the page, ever.

**Consolidated per-universe motion behavior** (previously six separate lists, now one reference):

| Universe | Reduced Motion disables | Still fully accessible |
|---|---|---|
| Home | Scroll-scrub on Identity In Progress (→ static crossfade sequence), cursor-velocity glitching, parallax → simple fade-in | All band content, achievement list |
| About Me | CRT overlay, idle character animation (freezes on a resting pose), window-drag inertia | All window content via keyboard/tab, non-drag open paths |
| Projects | Ambient camera drift, arm's autonomous idle movement, rover mission autoplay (→ static end-state + info panel) | Full List View, all project info |
| Blog | Orbit's ambient drift, ASCII-decode transition (→ simple crossfade) | List View, full post content |
| Resume | Dynamic Ink scroll-drawing (→ simple fade-in), entry/exit sequence staging (→ crossfade) | All document sections, semantic HTML |
| Contact | Sky drift, signal-trail cursor effect, submission glitch sequence | Full form functionality, constellation is decorative-only |

---

## 3a. Cognitive Load Reduction Mode (V2, distinct from Motion — and distinct from Low-Vision Mode, §2)

A different axis from Motion (§3) entirely — Motion is about *movement*; this is about *visual complexity and decoration count*, and the two are independent (a visitor might want full motion but simplified decoration, or the reverse). Also distinct from Low-Vision Mode (§2), which targets legibility/clarity rather than complexity/stimulation — the two address different needs and are independently toggleable, not two names for the same setting. When enabled: decorative side panels hide, layouts simplify, each universe's "ambient life" system (About Me's wallpaper state changes, Projects' inhabited-room surprises, Home's twinkling stars) disables entirely, simultaneous animations reduce, and non-essential easter-egg indicators disappear. Genuinely useful for ADHD, autism, brain fog, or general fatigue — visitors who want the real content without the site's full sensory density. Scoped V2 since it touches every universe's decorative layer and deserves real design attention per universe, not a single blanket CSS override.

## 3b. Reading Focus Tools (V2)

Particularly valuable for Blog and Resume, the two most text-dense universes:

- **Reading Ruler** — highlights a single horizontal line, moving with scroll or cursor.
- **Reading Mask** — dims surrounding content, spotlighting a focused region.
- **Focus Paragraph** — highlights whichever paragraph is currently being read.

Useful for dyslexia, ADHD, and visual tracking difficulties. Scoped V2 — genuinely valuable, but a real, distinct feature to build (not a CSS toggle), correctly sequenced after the core reading experience in both universes is stable.

---

## 4. Screen Reader & Semantic Structure

- **Real semantic HTML everywhere**, not just where it's convenient — every universe's stylized "paper," "window," "archive card," or "signal terminal" is a real heading, list, button, or form element underneath its visual skin, never a styled `<div>` masquerading as one. Concretely, per universe: Home resolves to `Main / Hero / Achievements / Projects / Footer`; Blog resolves to `Archive / Article List / Current Article / Outline / References`; Resume resolves to `Experience / Skills / Achievements / Education / Leadership`. A screen reader should never encounter an unlabeled `<canvas>` with no semantic equivalent — every visual/spatial element has a real structural counterpart.
- **Skip Navigation Links (a genuine gap in the earlier draft):** visible-on-focus skip links at the start of every page — `Skip to Main Content`, `Skip to Navigation`, plus page-specific targets where warranted (`Skip to Blog Content`, `Skip to Contact Form`). Particularly important given how large Home is, how content-heavy Blog can become, and how many sections Resume contains — without these, a keyboard or screen reader user has to tab through the entire shared nav on every single page load.
- **Real landmark roles, not just informal structure:** `<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>` used correctly and consistently across every universe, plus `role="search"` on search inputs (Blog's search, the command palette) and `role="region"` with `aria-labelledby` on major named sections (each universe's numbered/titled content blocks). This is what actually lets a screen reader user jump directly between regions rather than tabbing linearly through everything.
- **Alt text for content that looks decorative but isn't:** Resume's Achievement Stamps, Blog's ASCII illustrations, and any similarly stylized-but-meaningful visual all need real, descriptive alt text — a wax-seal graphic or dot-matrix mountain scene is still conveying real information, not just texture.
- **List Views as complete alternate paths** (Projects' 3D room, Blog's Orbit) — built to full parity with the spatial experience, not a summary of it.
- **Tab order and focus management:** every universe's draggable/spatial elements (About Me's windows, Blog's Orbit windows, Projects' room objects) need a sensible, logical tab order that doesn't depend on drag interaction to reach.
- **Terminal inputs are real `<input>` elements** (About Me's `system_info.sh`, Resume's `clearance` command, Blog's command palette) — properly labeled (`aria-label`), never a styled div faking an input.
- **Live regions for dynamic confirmations** — Contact's submission confirmation, Blog's autosave status, and similar should use `aria-live` appropriately so a screen reader user is actually notified when something completes, not left to guess.
- **Full forms-accessibility flow, not just the confirmation moment (a genuine gap in the earlier draft):** Contact's form is a hard functional requirement (per the Contact spec), so its accessibility needs to cover the entire lifecycle, not just "it has labels." Every field needs a real associated `<label>`, a description where helpful, and — critically — validation and error states announced via `aria-invalid` and `aria-describedby`, with submission success/failure announced via `aria-live`. A visually obvious red border on an invalid field means nothing to a screen reader user if the invalid state isn't also announced programmatically.

---

## 5. Colorblind Modes — Technical Approach & Known Risk Areas

- **Implementation:** SVG `feColorMatrix` filter definitions applied as a single screen-space filter (per the locked architecture) — cheap, GPU-accelerated, works uniformly across every universe without per-component recoloring work.
- **Specific risk areas already flagged, worth testing deliberately rather than assuming the generic filter handles everything:** Projects' constellation/portal star-field visual language (small bright points against a dark background — a pattern that's genuinely hard to parse under some colorblind conditions if contrast isn't deliberate) was already flagged in the Projects spec as needing specific testing. The same risk likely applies to Home's Achievement Constellation and Contact's signal-star visuals, since all three use the same small-bright-dots-on-dark-background language. Test all three specifically, not just the one that happened to get flagged first.
- **The more robust underlying principle — never rely on color alone (WCAG 1.4.1), which matters more than the correction filter itself:** a colorblind filter helps after the fact, but the better fix is designing so color was never the *only* signal in the first place. This is already true in a few places without having been named as a deliberate accessibility principle until now — worth stating explicitly so it's applied consistently going forward: Resume's tiered achievement stamps (§8 of the Resume spec) already differentiate importance by **size**, not color alone; Contact's Signal Strength system should differentiate by **brightness, pattern, and animation**, not color alone; Home's Achievement Constellation should differentiate primary vs. secondary stars by **shape, size, and position**, not color alone. Treat this as a checklist item for any new visual distinction added later, not just the three examples already covered.

---

## 6. Audio Accessibility

Full detail in the Audio Bible — summarized here for completeness:

- Four independent category volumes (Music/Ambience/Effects/Voice), plus the global on/off in the Control Center (§2).
- `prefers-reduced-motion` does **not** automatically mute audio — motion and sound are independent accessibility concerns, and conflating them would incorrectly silence a visitor who only wanted less visual motion.
- **Audio Descriptions (V2):** pre-written, recorded narration of a universe's core visual experience — real production work, correctly sequenced after core launch.
- **A distinct, second thing worth not confusing with the above — literal Text-to-Speech (V2, optional):** a "read this page aloud" feature using the browser's native Web Speech API to synthesize the actual page text on demand, rather than pre-recorded narration of visual experiences. These solve different problems: Audio Description narrates *what's happening visually* (for someone who can't see the animation); TTS reads *the actual text content* aloud (useful for a visitor who prefers listening to reading, independent of vision). Worth having both eventually, worth not building either before the underlying content is stable, and worth not treating them as the same feature.
- **Every important audio cue needs a visual equivalent, and the reverse holds too:** Contact's constellation tone (§6, Audio Bible) pairs with a visible star pulse; Resume's wax-seal snap pairs with a visible seal-crack animation. No piece of information should exist through sound alone, since a visitor with audio off or hearing loss needs the same information visually, and vice versa for audio descriptions (§8/above).
- **Voice Navigation (V2, lowest priority of this document's V2 items):** optional spoken commands ("Go Home," "Open Blog," "Increase Text Size") via the browser's `SpeechRecognition` API — genuinely optional, never required, and worth being honest that this is more speculative than the other V2 items here. A portfolio site doesn't have the same hands-free-use-case density as, say, a cooking app or a car interface, so this is worth having on the roadmap without treating it as equally load-bearing as audio descriptions or reading focus tools.

---

## 7. Touch & Motor Accessibility

**Input Modality Independence — the overarching principle, stated explicitly rather than left implicit:** any action possible with a mouse must also be possible with a keyboard alone. This has existed as an implicit rule across every universe's own accessibility notes (non-drag paths, List Views, tab order) — worth stating once, plainly, as the governing principle those individual rules all derive from: Home's Achievement Constellation, About Me's draggable windows, Projects' Orbit-style room navigation and IK-driven arm, Blog's draggable Orbit windows and notes — every one of these has a mouse-driven primary interaction, and every one of them needs a complete keyboard-operable equivalent, not a partial one.

- **44×44px minimum touch targets**, site-wide, per your original brief — applies to every interactive element on every universe, not just the ones already called out per-spec (Projects' room objects, Contact's form fields and icons).
- **Every drag interaction has a non-drag equivalent:** About Me's window dragging, Blog's window/note dragging, Projects' arm-joint dragging — none of these should be the *only* way to access their underlying function. A visitor using only a keyboard, or with limited fine motor control, needs an alternate path (tab-and-activate, a button, or a List View) to everything a drag gesture would otherwise gate.
- **No interaction should require a specific gesture speed or precision** — Home's cursor-velocity-triggered glitch effect is a bonus flourish layered on top of content that's otherwise fully readable at rest, never a gate a visitor has to move fast enough to trigger.

---

## 7a. Seizure & Flash Safety (hard rule, V1, non-negotiable)

A genuine gap worth stating as plainly as any WCAG requirement in this document, since it wasn't explicitly covered before: **no animation on this site may flash more than three times per second, occupy more than 25% of the viewport with a rapid luminance change, or produce repeated full-screen flashes** (per WCAG 2.3.1). This applies specifically to the effects most likely to brush up against this limit: Home's glitch effects, Blog's ASCII-decode transition, Contact's signal glitches, and the Transition Bible's shared `glitch` effect used across the Corridor. Every one of these needs an explicit flash-rate audit before launch — not an assumption that "it's just a glitch effect, it's probably fine." This is a hard, testable rule, not a design preference.

---

## 8. Low-End Devices (a distinct concern from bandwidth, §9)

Worth separating explicitly, since device capability and connection speed are different signals that don't always correlate — a low-end laptop on fast office wifi, or a flagship phone on poor rural signal, are both real cases the site needs to handle correctly:

- **Feature flags gate the heaviest scenes** (Home's face mesh, Projects' R3F room and IK arm) with genuine fallbacks, not just a loading spinner that never resolves — a simpler crossfade sequence for Home's Identity In Progress, a static parallax-illustration version of Projects' arm, per each universe's own spec.
- **A manual "Lite Experience" option, independent of automatic detection:** `navigator.connection.effectiveType` (§9) detects connection speed, not GPU/CPU capability. A device could have excellent bandwidth and still struggle to render R3F scenes smoothly. Worth offering a manual toggle (in the Control Center or as a one-time suggested banner) rather than relying solely on connection-based detection to decide when to serve the lighter experience.
- **60fps desktop / stable 60fps mobile / 30fps acceptable floor on confirmed low-end devices** — the specific performance budget already established in the Home and Projects specs, worth treating as a site-wide target rather than universe-specific.

---

## 9. Low Bandwidth / Slow Connections

- **`navigator.connection.effectiveType` detection** (per the locked `lib/network-tier.ts`) — on a detected 2G/3G connection, surface a **"Lite Experience Recommended"** suggestion rather than silently degrading or silently forcing the full experience on a connection that can't comfortably support it.
- **Asset tiering per universe** — each universe's asset manifest (per the locked architecture) supports lazy-loading and prefetching; on a slow connection, load each universe's critical content first and defer heavier illustration/3D/audio assets until requested or near-viewport.
- **Progressive enhancement, not a broken experience:** a visitor on a genuinely poor connection should get a slower-loading but *complete and correct* site, not a partially-broken one — content should never depend on an asset that might not have finished loading yet.

---

## 9a. Offline & Failure Resilience (a distinct concern from slow-but-working, V1)

Distinct from §9's "the connection is slow" — this section is about genuine runtime failure: an asset that 404s, a WebGL context that crashes, a script error, a browser that doesn't support something expected. **No universe should ever show a blank or broken screen as a result.** Every heavy or failure-prone piece already has a natural fallback established elsewhere in this project — this section just states the rule once, explicitly: if Home's face mesh fails to render, fall back to a static portrait image. If Projects' R3F scene fails, fall back to List View. If Blog's Orbit view fails, fall back to List View. If Contact's constellation canvas fails, the form still works normally, with no visual dependency on the canvas having succeeded. If any audio fails to load, the site falls back to silent — never a broken audio player or a console error visible to the visitor. Treat "what does this look like when it fails" as a real, tested case for every heavy feature in this project, not an assumption that failure won't happen.

---

## 9b. Accessibility Across Universes (quick reference)

A compact "at a glance" summary of each universe's primary accessibility risk and its solution — pointing back to the fuller detail already documented (§3's motion table, §5's colorblind risk areas, and each universe's own spec), not duplicating it:

| Universe | Primary Risk | Solution |
|---|---|---|
| Home | Motion overload (scroll-scrub, cursor-velocity effects) | Three-tier Motion controls (§3) |
| About Me | Window/desktop complexity, drag-dependent interaction | Keyboard tab order + non-drag paths (§7) |
| Projects | 3D spatial interaction (room, arm, mirror) | Full List View, complete parity (§0) |
| Blog | Dense reading, spatial Orbit view | List View + Reading Focus Tools (§3b) |
| Resume | Visual "document" styling over real content | Real semantic HTML underneath (§4) |
| Contact | Decorative signal/constellation effects | Standard, fully-functional form fallback (§0, §4) |

---

## 10. Cognitive Accessibility

- **Dyslexia-Friendly font option** (§2) — a real typeface swap, not a cosmetic tweak, applied consistently to all body text across every universe.
- **Text Size scaling** (§2) that actually reflows layouts correctly at every size, tested at the largest setting specifically, since that's where layouts are most likely to break.
- **The master rule, repeated across every universe spec until now — stated once, completely, here:** hidden/discovery-based content (Resume's UV-light and coffee-stain easter eggs, Blog's Ghost Posts, Projects' Research Board patent reveals and Mission Control door, About Me's `secret.exe` and Recycle Bin) is always flavor, personality, or bonus depth — **never** the only path to information a visitor actually needs to evaluate you, use the site, or understand its content. This has been stated independently in all six universe specs; consolidating it here as one non-negotiable rule rather than six separate promises that could drift out of sync during implementation.

---

## 11. Testing & CI

- **`axe-core`** in development (`@axe-core/react`), catching violations as they're introduced, not discovered later.
- **`jest-axe`** in the unit test suite, per universe.
- **Lighthouse CI** in the deployment pipeline (per the locked architecture's GitHub Actions flow) — accessibility and performance scores are checked on every PR, not just before major releases, so regressions are caught immediately rather than accumulating silently.
- **A manual pass is still necessary** — automated tools catch real issues but don't catch everything (e.g. whether an alt text description is actually *meaningful*, or whether a tab order is *sensible* rather than merely present). Budget real manual testing time, not just a passing CI badge.

### 11a. Pre-Launch Manual Testing Matrix

The concrete acceptance checklist that turns "we did a manual pass" into something verifiable — every scenario below must pass across the entire site, not just a sampled few pages:

| Scenario | Must Pass |
|---|---|
| Keyboard only (no mouse) | Entire site |
| NVDA (screen reader) | Entire site |
| VoiceOver (screen reader) | Entire site |
| 400% browser zoom | Entire site |
| High Contrast mode | Entire site |
| Reduced Motion | Entire site |
| Lite Experience | Entire site |
| Simulated 3G network | Entire site |
| Audio muted | Entire site |
| WebGL unavailable/disabled | Entire site |

### 11b. Accessibility Analytics (V2, privacy-conscious)

An optional, genuinely interesting addition given this is a portfolio site you'll want to actually learn from: anonymous, aggregate tracking of *which* accessibility features get used (Reduced Motion enabled, High Contrast enabled, Lite Experience enabled, colorblind modes activated) — never tied to individual visitor identity or session-level behavior. This should ride on the same privacy-respecting analytics choice already locked for the rest of the site (Plausible, per the stack doc), not a separate tracking system — the goal is understanding whether these features are actually being used and worth the investment, not surveilling how any individual visitor browses. Scoped V2 since it's genuinely optional and not required for the accessibility features themselves to work correctly.

---

## 11c. The Accessibility Narrative

Everything above is engineering and testing detail — worth closing with the reason it matters, tying this document to the same storytelling logic the Transition and Audio Bibles already establish. Home, About Me, Projects, Blog, Resume, and Contact tell one continuous story — identity, memory, creation, knowledge, evidence, connection. **That story is carried by content, structure, interaction, and meaning — not by visual effects alone**, which is exactly why the Layer model in §0 works: Presentation is optional precisely because it was never what the story actually depended on.

A visitor using Reduced Motion, High Contrast, a screen reader, keyboard-only navigation, Lite Experience mode, and a slow connection simultaneously — every accessibility constraint in this document stacked at once — should still come away from the site understanding who you are and what you've built, in the same order and with the same emotional throughline as a visitor experiencing every particle effect at full fidelity. The real test of this entire project was never whether a high-end gaming PC can render every effect smoothly. It's whether someone on an old laptop, using a screen reader, on a slow connection, with every motion setting turned down, still experiences the same six-universe journey and understands the same person at the center of it.

---

## 12. V1 / V2 Scope

Given that WCAG AA and Lighthouse targets were stated as hard requirements from the very start of this project, very little here is legitimately deferrable — most of what's V2 elsewhere in this document is V2 because it's genuinely extra (audio description, literal TTS), not because baseline accessibility itself is negotiable.

**V1 — Launch Critical (non-negotiable)**
- The full Accessibility Control Center (§2, including cursor size/contrast, colorblind preview mode, and Low-Vision Mode), the Keyboard Shortcuts & Navigation Matrix (§2a), the three-tier Motion system (§3), all semantic/screen-reader requirements including concrete per-universe structure, Skip Navigation Links, and real landmark roles (§4), full forms-accessibility validation flow for Contact (§4), colorblind filters with the flagged risk areas and the "never rely on color alone" principle (§5), audio category volumes and the reduced-motion/mute independence rule (§6), 44×44 touch targets, non-drag equivalents, and the explicit Input Modality Independence principle (§7), Seizure & Flash Safety (§7a — a hard rule, not a preference), feature-flag fallbacks for low-end devices (§8), network-tier detection and Lite Experience suggestion (§9), Reflow at 320px/400% zoom (§1), Offline & Failure Resilience (§9a — no blank screens, ever), the Accessibility Across Universes quick-reference (§9b), dyslexia font and text scaling (§10), the master "never gate essential content" rule (§10), axe-core/jest-axe/Lighthouse CI (§11), and the full Pre-Launch Manual Testing Matrix (§11a).

**V2 — Recommended, Not Required for Launch**
- Full Audio Descriptions (§6/Audio Bible §8) — real production overhead.
- Literal Text-to-Speech via Web Speech API (§6) — a genuine, distinct enhancement, not required since real semantic HTML already makes the site screen-reader compatible without it.
- Voice Navigation (§6) — the lowest-priority item in this document; genuinely optional and more speculative than the other V2 items.
- Cognitive Load Reduction Mode (§3a), Reading Focus Tools (§3b), and Low-Vision Mode (§2) — all valuable, all real feature work touching every universe's decorative or typographic layer, correctly sequenced after V1 ships. The three are independently toggleable and address genuinely different needs (complexity/stimulation, reading tracking, and legibility/clarity respectively) — worth building as three distinct settings, not collapsing into one "extra accessibility stuff" toggle.
- The manual Lite Experience toggle (§8) beyond automatic connection-based detection — nice additional control, not a blocker if automatic detection ships first.
- Accessibility Analytics (§11b) — genuinely optional, privacy-conscious, not required for the features themselves to function.
