# The Mobile Bible — Complete Mobile Experience Specification

Reference: every universe's own Mobile Adaptation section, the Transition Bible, the Accessibility Bible

This is the last of the four documents planned back when the architecture was first locked (Transition Bible, Audio Bible, Mobile Bible, Asset Production List) — all four now complete. Like the others, this one exists to consolidate what's already scattered across six universe specs and add the handful of things that only make sense described once, at the whole-site level: the shared mobile shell, the touch gesture vocabulary, and — most importantly — a few real gaps that only become visible when you look at mobile as one coherent system rather than six separate adaptations.

---

## 0. Core Philosophy: Mobile-Native, Not Desktop-Shrunk

Every universe's Mobile Adaptation section already follows the same underlying instinct — Aero-OS's free-floating windows become a stacked card sequence; Blog's spherical Orbit becomes a vertical list; Projects' free camera exploration becomes drag-to-look-plus-tap. None of these are the desktop experience made smaller. Each is the same universe's *actual content and interaction model*, re-expressed in a genuinely mobile-native vocabulary — vertical scroll, bottom-anchored navigation, tap as the primary gesture.

**One thing worth stating plainly, since it's not obvious from reading the universe specs in isolation:** the site's transition system (Transition Bible) was largely designed *from* mobile reference material in the first place — the original transition storyboard that established the six primary sequences (glitch→collapse→reform, error→break-apart→illustrate, flatten→pixelate→ASCII-form, and so on) was mobile mockup material. Desktop transitions were built to match that reference, not the other way around. So unlike most of this document, where mobile is an adaptation of a desktop-first design, transitions are a case where mobile fidelity *is* the reference standard.

---

## 1. The Mobile Shell

### 1.1 Bottom Tab Bar (primary navigation)

Every universe replaces the desktop top nav with a bottom tab bar as primary navigation on mobile — confirmed across every universe's mobile mockup, not a new proposal. Structurally identical across all six universes (six tabs: Home/About/Projects/Blog/Resume/Contact), re-skinned per universe's own visual language (icon style, active-state color, typography) the same way the top nav is re-skinned on desktop. Bottom placement specifically because it's within comfortable thumb reach, unlike a top nav on a tall phone screen.

### 1.2 Multiverse Navigator (mobile behavior)

On desktop, the Navigator is a persistent floating widget that auto-minimizes during immersive moments. On mobile, screen real estate is too precious for a persistently-visible floating element even at small size — **it collapses to a small icon by default** (docked near a screen edge, out of the way of the bottom tab bar) and expands into the full universe map on tap, rather than being always partially visible. Same underlying data (`% explored`, universe map), different default visibility state.

### 1.3 Command Palette (mobile trigger — a genuine gap, now resolved)

`⌘K`/`Ctrl+K` doesn't exist on a touchscreen, and no universe spec addressed what replaces it on mobile — worth resolving explicitly rather than leaving the command palette desktop-only. A small search/command icon lives in the bottom tab bar area (or as a floating action button, docked opposite the Navigator icon from §1.2) — tapping it opens the same command palette interface, just triggered by touch instead of a keyboard shortcut. The palette's actual functionality (searching posts/projects/skills, quick navigation) is unchanged; only the trigger mechanism differs.

### 1.4 Accessibility Control Center (mobile placement)

Reachable via the same icon-based trigger as desktop (part of the shared shell), opening as a full-screen or bottom-sheet panel on mobile rather than a floating panel — appropriate given how many settings it contains (Accessibility Bible §2) and how little screen space a phone has for a floating overlay.

### 1.5 Safe-Area Policy

Worth making explicit rather than leaving implied by the bottom tab bar's placement: every fixed-position UI element — the bottom tab bar, the Navigator icon, the command-palette trigger — must respect `env(safe-area-inset-*)` CSS, so nothing sits under a notch, a Dynamic Island, a gesture-navigation bar, or a camera cutout. No critical tap target should ever land somewhere the OS itself is visually or functionally occupying. On foldables specifically, avoid placing critical UI directly across the fold line where it's practical to avoid — a minor consideration, but a cheap one to account for now rather than patch later.

---

## 2. Touch Gesture Vocabulary

A deliberately small, consistent set — worth stating explicitly so no universe invents a gesture that conflicts with another's:

| Gesture | Meaning | Used by |
|---|---|---|
| Tap | Primary action — open, select, activate | Every universe |
| Drag-to-look-around | Constrained camera orbit (not free 6-DOF movement) | Projects only |
| Swipe | Pagination through a set of items | Projects' object browsing, potentially Blog's list |
| Long-press | **Not used anywhere on this site** | — |

Keeping long-press explicitly unused avoids gesture ambiguity — a visitor never has to wonder whether holding their finger down does something different from a quick tap.

### 2a. Haptic Feedback

A brief mention, worth establishing as a consistent philosophy rather than leaving ad hoc: light haptic feedback (via the Vibration API where supported) accompanies genuine completion moments — a successful form submission, landing after a universe transition, an accordion opening or closing, an achievement stamp revealing itself, a command-palette selection. Same restraint as every other effect on this site — subtle confirmation, never a novelty buzz — and it must fail silently where unsupported (iOS Safari's haptics support is limited) rather than erroring or blocking the action it was meant to accompany.

---

## 3. Per-Universe Mobile Adaptation (consolidated reference)

Full detail lives in each universe's own spec — this table exists so mobile behavior can be reviewed as one system, the way §3 of the Audio Bible did for sound.

| Universe | Desktop interaction | Mobile equivalent |
|---|---|---|
| Home | Scroll-band narrative | Same scroll bands, scaled — no structural change, since scroll is already mobile-native |
| About Me | Free-floating draggable windows | Vertically stacked, non-draggable card sequence (My Story.exe → PowerDVD → Things I Like → Robotics.iso → Football.mp4 → Future.txt → Terminal → Memories.zip); window chrome kept for visual continuity, drag/minimize buttons inert; terminal and PowerDVD stay genuinely interactive |
| Projects | Free-ish camera + click objects | Drag-to-look (constrained orbit) + tap opens a bottom card (title, description, tech tags, View Project, bookmark); swipeable pagination across objects |
| Blog | Spherical Orbit arrangement | Vertically stacked list of post windows, same window-chrome visual language; Archive Map widget persists; individual posts keep full desktop layout in one scrollable column |
| Resume | Six stacked "documents" | Vertical accordion, collapsed by default (icon + one-line description + `VIEW →`); portrait/metadata/wax seal stay visible above the fold regardless of what's expanded |
| Contact | Three-layer parallax scene | Compresses to one scrollable column (hero → terminal card → contact methods → closing footer); the `01—02—03` connection-sequence indicator persists during entry |

---

## 4. Mobile Transitions

Per §0's note — these were mobile-first by construction, so this section is closer to a restatement than a new adaptation. The six primary transitions (Transition Bible §4) play identically on mobile, triggered by tap instead of click, with the same cursor-mutation-equivalent (though mobile has no persistent cursor to mutate — see §5 for how this resolves). The Multiverse Corridor (Transition Bible §2a) and Transit Tone (§7 there) are unchanged — both are screen-space/audio effects with no dependency on pointer-based input.

---

## 5. A Real Gap: Cursor-Dependent Interactions on Touch Devices

Several interactions across the site are specified in terms of cursor *position* or cursor *proximity* — which has no direct equivalent on a touchscreen, where there's no persistent pointer hovering over content between taps. This wasn't addressed systematically in any single universe spec; worth resolving once, here, rather than leaving six separate silent gaps:

| Cursor-dependent interaction | Universe | Mobile resolution |
|---|---|---|
| UV-light hover reveal (hidden text on Resume) | Resume | **Needs an explicit alternative** — recommend a small toggleable "UV Light" button near the affected area, which the visitor taps to reveal the hidden text directly, rather than trying to simulate hover with touch |
| "Notices you" cursor-proximity reactions (About Me's figure, Projects' figure, Contact's astronaut) | About Me, Projects, Contact | These are decorative flourishes, not required content — acceptable to simply not trigger on mobile, since nothing essential depends on them |
| Cursor signal trail | Contact | Not applicable on touch — acceptable to omit; a visitor on mobile never had a persistent cursor to leave a trail from |
| Context-aware cursor glyphs (⚙/✎/>) | Projects | Not applicable — mobile's tap-to-open-card interaction (§3) already tells the visitor what an object is without needing a cursor-glyph preview |
| Colorblind/accessibility hover previews (Navigator hover, nav item preview) | Site-wide, Transition Bible §7a | Downgrade to tap-and-hold-briefly or simply omit the preview — these were always enhancement-tier, not required |

**The one item in this table that actually needs new design work, not just "acceptable to omit," is Resume's UV-light reveal** — since it's specced as genuine bonus content, not a required element, it deserves a real mobile-equivalent trigger rather than silently disappearing on touch devices.

---

## 6. Keyboard-Avoidance for Forms & Text Input

A concrete, common mobile-web failure mode worth flagging explicitly rather than assuming it'll be handled by default: focusing a text input (Contact's form fields, About Me's terminal, Blog's command palette, Resume's `clearance` command) triggers the mobile virtual keyboard, which covers a large portion of the screen — often the exact area containing the field being typed into, or the submit button below it. Every text-input surface on mobile needs to either scroll the focused field into the visible area above the keyboard, or use a layout that doesn't place critical actions where the keyboard would cover them. This is a real, specific QA item, not a general "mobile forms should work" statement.

---

## 7. Orientation Handling

Not addressed in any universe spec individually — worth a site-wide default: the site targets **portrait as the primary orientation** (how a phone is actually held for browsing), but must not break in landscape. Most universes' vertical-stack mobile adaptations (§3) work fine rotated, since they're just narrower columns either way. **Projects is the one universe where landscape might genuinely be preferable** — the drag-to-look-around room interaction benefits from more horizontal viewport — worth allowing free rotation there rather than locking orientation, even though portrait remains the default assumption everywhere else.

---

## 8. Performance & Network on Mobile

Mobile visitors are disproportionately likely to be on a cellular connection and a less powerful device than a desktop visitor — the Lite Experience system (Accessibility Bible §8–9) already exists site-wide, but on mobile specifically, the automatic connection-based and device-based detection (`navigator.connection.effectiveType`, feature flags gating R3F scenes) should lean toward the lighter tier more readily than the desktop default would. Home's face mesh and Projects' full room are the two heaviest assets on the site — both already have documented fallbacks (static portrait, List View) — mobile is where those fallbacks matter most in practice, not an edge case to handle eventually.

---

## 9. Accessibility on Mobile

Everything in the Accessibility Bible applies identically on mobile — 44×44px touch targets (already a mobile-specific requirement from the original brief), the three-tier Motion system, Skip Links, and the Reflow requirement (320px width, per Accessibility Bible §1) are especially relevant here since mobile viewports are naturally closer to that width than desktop ever is. Nothing new to add here beyond confirming the Accessibility Bible was already written with mobile viewports in mind, not as a desktop-only afterthought.

---

## 9a. State Restoration

Mobile visitors get interrupted far more often than desktop ones — a phone call, switching apps, the lock screen, or the browser simply suspending a backgrounded tab are all routine, not edge cases. Where practical, a visitor returning from one of these interruptions should find the site roughly as they left it: current universe, scroll position, which Resume accordion sections were expanded, an open About Me window's position, and (where it makes sense) in-progress command-palette state. This uses the same lightweight, session-only persistence pattern already established elsewhere on the site (About Me's window positions, Blog's Orbit drag positions) — not a new backend mechanism, just applying that existing pattern to the specific case of a mobile visitor coming back after being interrupted rather than only to a same-session page reload.

---

## 10. V1 / V2 Scope

**V1 — Launch Critical**
- The bottom tab bar (§1.1), the mobile command-palette trigger (§1.3), the Safe-Area Policy (§1.5 — cheap, prevents real device-specific bugs), all six universes' Mobile Adaptation as already specced (§3), mobile transitions (§4, already largely built via the mobile-first reference material), the UV-light mobile-equivalent trigger (§5 — a real content-access gap, not cosmetic), keyboard-avoidance for all text inputs (§6), portrait-default/no-breakage-in-landscape (§7), and State Restoration for the common interruption cases (§9a — session-only, reusing an existing persistence pattern).

**V2 — Recommended, Not Required for Launch**
- Haptic Feedback (§2a) — genuinely cheap, but dependent on browser API support fragmentation, and easy to layer on after the core interactions it accompanies already work without it.
- Free rotation support specifically for Projects' landscape-friendly room view (§7) — nice, not blocking, since portrait mode still fully functions there via the same drag-to-look interaction.
- A lightweight PWA/add-to-homescreen manifest — a small, genuinely optional nicety given the site's ambition, not a feature this document is recommending be prioritized; mentioned here only so it's on record as a considered option rather than an oversight.
