# The Light/Dark Bible — Complete Alternate-Version Specification

Reference: every universe's own Dark Mode / Light Mode section, `lib/theme/resolver.ts`, the Accessibility Bible

Light and dark mode have been specced individually, universe by universe, throughout this whole process — and like every other cross-cutting concern in this project, that scattered treatment hides a few decisions that only make sense addressed once, at the whole-site level: where the toggle actually lives, what happens in the moment of switching, and how six independently-designed "alternate versions" add up to one coherent system rather than six unrelated light switches.

---

## 0. Core Philosophy: Not an Inversion, a Different Self

Stated individually in every universe spec, worth stating once, completely, here: **no universe's dark mode is a recolor of its light mode.** Each is a deliberately different version of that universe — different mood, sometimes a different named identity, occasionally a different literal in-world explanation for why it looks the way it does. The weakest possible version of this feature would be "swap a few CSS variables." That was rejected everywhere it came up.

**The six universes sit on a real spectrum, not all doing the same thing to the same degree** — worth naming explicitly since it's an interesting, consistent pattern once you see it across all six specs:

| Universe | Where it sits | What actually changes |
|---|---|---|
| Home | **Full alternate identity** | Light = Earth-Prime; Dark = **NEXUS**, a named alter-ego with its own wordmark, tagline ("Not a hero. Just a reflection of what I'm becoming."), and a genuinely different visual treatment of the hero portrait — not the same image recolored |
| About Me | **Full alternate identity** | Light = the daytime rooftop, dusk/hope/future register; Dark = **AERO → MIDNIGHT OS**, late-night-coding/insomnia register, cooler glass chrome, neon lighting — same desktop, same windows, different emotional address entirely |
| Blog | **Different physical medium, same identity** | Dark = an active digital archive/terminal; Light = a **recovered physical research archive** — windows become documents, ASCII art reads as printed logs, real paper-decay details (stamps, coffee rings, correction marks). Same brutalist bones, genuinely different material |
| Resume | **Different decay story, same identity** | Light = sun-bleached brown paper; Dark = charred black paper with amber ink. A literal narrative justification: the dossier survived a fire in one reality, sun exposure in the other |
| Projects | **Time-of-day shift, same identity** | The window swaps from sunflower daylight to a night sky with visible moon/stars; interior lighting shifts warmer/cooler to match |
| Contact | **Time-of-day shift, same identity** | Neon night vaporwave (dark, default) vs. a **lavender dawn transmission** (light) — same astronaut, cat, hand, and terminal, cooler pastel tones |

No universe is "wrong" for sitting lower on this spectrum — Projects and Contact's time-of-day framing is exactly appropriate for what those universes are; Home and About Me earning a full alternate identity fits their more personal, identity-centered subject matter. The spectrum is a description of what already got specced independently, not a rule that every universe must reach the top of it.

---

## 1. The Global Toggle

**One preference, not six independent ones.** A visitor sets light or dark once; that single value is passed as the `mode` parameter into every universe's own `resolveTheme(universe, mode, season)` call as they navigate — each universe already has both variants fully defined, so the same global toggle produces NEXUS on Home, MIDNIGHT on About Me, the recovered-archive look on Blog, and so on, without needing six separate settings a visitor would have to configure individually.

- **First-visit default:** respect the system's `prefers-color-scheme`, consistent with the "respect system settings" principle already established in the Accessibility Bible — not a hardcoded default ignoring what the visitor's OS already tells the browser.
- **Placement:** part of the shared shell, reachable from every universe identically — a simple sun/moon-style icon near the universe indicator in the top nav (desktop) / bottom shell (mobile), not buried inside the Accessibility Control Center.
- **Distinct from High Contrast (Accessibility Bible §2) — genuinely two different settings, not the same toggle wearing two names:** Light/Dark is a *creative* choice (which of two designed identities you're looking at); High Contrast is an *accessibility* feature (targeting real 7:1+ contrast ratios for low-vision visitors). The two combine independently — a visitor can have Dark + High Contrast active simultaneously, meaning each universe needs **four** total token variants defined, not two: light-normal, light-high-contrast, dark-normal, dark-high-contrast. Worth stating explicitly since it's easy to accidentally build only two and discover the gap late.
- **Persistence:** the same lightweight, already-established site-wide pattern (local storage, not a backend round-trip) — the choice survives navigation and return visits without needing to be reset per universe.

---

## 2. The Mode-Switch Transition (new — an instant swap undersells this feature)

Given how far several universes take this (Home and About Me are genuinely different *selves*, not recolors), an instant, flat CSS-variable swap the moment the toggle is pressed would undersell the feature — it would make NEXUS feel like a filter rather than an alternate identity. **Toggling light/dark plays a brief transition, reusing the existing shared effects library (Transition Bible §2) rather than inventing a new one:** a quick `glitch` flicker, resolving into the new mode's resolved theme — the same visual grammar already used for crossing between universes, applied here to crossing between a universe's two selves. Deliberately brief (a few hundred milliseconds, not a full multi-stage choreography) — this is a mode switch on the page you're already viewing, not a trip to a different universe, and shouldn't take as long as one.

Respects `prefers-reduced-motion`, per the Accessibility Bible's global rule — collapses to a simple cross-fade, consistent with how every other transition on the site handles that preference.

---

## 3. Per-Universe Reference (consolidated)

Full detail lives in each universe's own spec — restated compactly here for whole-site review, the same pattern used in every other Bible in this set.

- **Home:** NEXUS — cracked black-and-white texture over the photo half, a glowing purple mask-eye motif replacing the wireframe half, the NEXUS wordmark, looser/more urgent handwritten annotation style.
- **About Me:** MIDNIGHT OS — true midnight sky (not dusk), cooler/darker glass chrome, neon rather than warm-glow lighting, a quieter and more introspective ambient soundtrack (Audio Bible §3.2).
- **Projects:** night sky with visible moon and stars through the window; interior lighting (string lights, lamp, monitor glow) shifts cooler to match.
- **Blog:** cream paper, blue ink, red accents; windows read as physical documents/folders; ASCII art reads as printed terminal logs; real paper-decay texture (stamps, coffee rings, correction marks, handwritten arrows).
- **Resume:** charred black paper, amber/orange ink; burned edge damage in place of sun-faded wear.
- **Contact:** lavender dawn — the sunset gradient shifts toward soft pastel dawn tones, neon accents cool into pastel cyan/lavender.

---

## 4. Confirmed Defaults (production-sequencing reference)

Worth noting which mode was designed/mocked *first* for each universe, since it affects asset production order (Asset Production List §10) — the confirmed-first variant is generally the safer one to build first, with the alternate following once the pattern is proven:

| Universe | Confirmed/mocked first | Alternate |
|---|---|---|
| Home | Light (Earth-Prime collage) | NEXUS (dark) |
| About Me | Light (daytime rooftop) | MIDNIGHT OS (dark) |
| Projects | Light (day/sunflower window) | Dark (night window) |
| Blog | Dark (terminal archive) | Light (recovered paper archive) |
| Resume | Light (sun-bleached paper) | Dark (charred paper) |
| Contact | Dark (neon night) | Light (lavender dawn) |

Four universes default-designed light-first, two (Blog, Contact) dark-first — this doesn't create any actual conflict for the global toggle (§1), since each universe has both variants fully defined regardless of which one happened to get mocked first; it's purely a note for whoever sequences the remaining art production.

---

## 5. Outstanding Design Work (consolidated from six separate flags)

Two real gaps, previously buried in individual specs, worth surfacing together so they're visible at a glance rather than discovered separately:

- **Home's NEXUS treatment is only fully designed for the Hero band.** Identity In Progress, Builder Manifesto, Achievement Constellation, and Universe Gateway's dark-mode-specific redesigns don't yet have real reference material — this is the single biggest remaining light/dark design gap on the site.
- **About Me's MIDNIGHT OS has a name and a thematic direction (late-night coding, insomnia, ideas at 2am) but no real visual design pass yet** beyond the wallpaper's tone. Both of these need actual design work before they can be considered "confirmed" the way Blog's and Resume's alternates already are.

---

## 6. V1 / V2 Scope

**V1 — Launch Critical**
- The global toggle (§1), the mode-switch transition (§2, respecting reduced-motion), all six universes' *already-confirmed* light/dark variants (Projects, Blog, Resume, Contact — fully specced and, in most cases, mocked), Home's light + NEXUS-hero-band, About Me's light + MIDNIGHT wallpaper tone, and the four-variant token requirement for High Contrast combination (§1).

**V2 — Recommended, Not Required for Launch**
- Completing Home's NEXUS treatment for bands 2–5 (§5) — launch is possible with Hero fully dark-designed and the remaining bands using a reasonable interim treatment, but this shouldn't stay unfinished indefinitely.
- Completing About Me's MIDNIGHT OS full visual design pass (§5) — same situation; the wallpaper tone alone isn't the finished feature.
