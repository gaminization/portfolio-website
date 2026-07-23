# Multiverse Portfolio — Architecture & Project Structure (LOCKED)

Companion doc to `multiverse-portfolio-stack.md`. This defines how the pieces fit together: folder structure, data flow, the theme/transition systems, and the contracts between them.

**Status: locked.** This merges three architecture passes into one final structure. Stop redesigning after this — build against it, and only revisit a specific piece if implementation surfaces a real problem, not a hypothetical one.

Key decisions locked in this version:
- Per-universe config folders (`universes/{name}/`), not one monolithic token file
- Seasonal theming as its own layer, merged on top of universe tokens at resolve time
- Legacy era modes (1998/2005/2012/plaintext) as a separate rendering system, not a theme
- Effect-based transitions (`glitch`, `collapse`, `portal`, etc.), composed per universe pair
- Split Zustand stores per concern
- Blog CMS lives inside this app (`app/admin`), not a separate deployment
- **Flat routes** (`/about`, `/projects`, etc.) — universe identity is a content/theming concern resolved from the route, not a URL segment. No `/universes/[universe]/about` nesting.

---

## 1. High-Level Architecture

```
┌───────────────────────────────────────────────────────────────────┐
│  Next.js App Router (flat routes: /, /about, /projects, /blog…)   │
│                                                                     │
│  ┌──────────────┐   ┌──────────────┐   ┌──────────────────────┐  │
│  │ Page Routes   │   │ Shared Shell  │   │ Blog CMS              │  │
│  │ (6 pages)     │◄──┤ (nav, cursor, │──►│ (Tiptap editor,       │  │
│  │               │   │  transitions, │   │  app/admin, in-app)   │  │
│  │               │   │  navigator)   │   │                       │  │
│  └───────┬───────┘   └───────┬──────┘   └──────────┬────────────┘  │
│          │                   │                      │               │
│          ▼                   ▼                      ▼               │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │  universes/{id}/config.ts   ×6, each exporting universeConfig   │ │
│  │            (theme, sounds, cursor, transitions, tokens)         │ │
│  │                          │                                       │ │
│  │                          ▼                                       │ │
│  │  themes/seasonal/*  ─────► resolveTheme(universe, mode, season)  │ │
│  │  (merged on top of the universe's base tokens at resolve time)   │ │
│  └────────────────────────────────────────────────────────────────┘ │
│                              │                                       │
│  legacy/{era}/  — bypasses all of the above entirely, gated by      │
│  <html data-era>, own stylesheets + simplified markup                │
└──────────────────────────────┼───────────────────────────────────────┘
                                ▼
                  ┌─────────────────────────┐
                  │  Postgres (Supabase)     │
                  │  posts, comments,        │
                  │  contact_submissions     │
                  └─────────────────────────┘
```

**Core principle:** every universe is a config object (`universeConfig`) applied to one shared shell (nav, cursor, transitions, navigator, accessibility panel). No universe owns its own copy of these systems, and no route encodes universe identity in its path — a page's universe is resolved from the route internally, the same way you'd resolve any other page metadata. This is what keeps six wildly different aesthetics from becoming six different codebases without also fragmenting your URLs.

---

## 2. Repo Structure (full, locked)

```
multiverse-portfolio/
│
├── app/
│   ├── layout.tsx                      # Root layout: providers, fonts, global shell
│   ├── globals.css                     # Tailwind base + CSS variable resets
│   │
│   ├── page.tsx                        # Home — Earth-Prime            (flat route: /)
│   ├── about/page.tsx                  # About Me — Aero-OS            (/about)
│   ├── projects/page.tsx               # Projects — Cuphead-verse      (/projects)
│   ├── blog/
│   │   ├── page.tsx                    # Blog archive — Brutalist Archive (/blog)
│   │   └── [slug]/page.tsx             # Individual post reader        (/blog/[slug])
│   ├── resume/page.tsx                 # Resume — Dossier              (/resume)
│   ├── contact/page.tsx                # Contact — Vapor-Signal        (/contact)
│   ├── universes/page.tsx              # Explicit universe map view    (/universes)
│   │
│   ├── admin/                          # Blog CMS — lives in this app, not a separate deploy
│   │   ├── layout.tsx                  # Auth check wrapper
│   │   ├── page.tsx                    # Post list / dashboard
│   │   └── posts/
│   │       ├── new/page.tsx
│   │       └── [id]/page.tsx           # Tiptap editor mounts here
│   │
│   └── api/
│       ├── contact/route.ts            # POST → Resend
│       ├── posts/route.ts              # CRUD for blog posts
│       ├── posts/[id]/route.ts
│       ├── posts/[id]/autosave/route.ts
│       ├── analytics/route.ts          # Custom event ingestion (or Plausible passthrough)
│       ├── search/route.ts             # Server-side fallback for command palette
│       └── auth/[...nextauth]/route.ts
│
├── universes/                          # ★ THE key folder — one per universe, identical shape
│   ├── earth-prime/                    # Home
│   │   ├── theme.ts                    # colors, type scale
│   │   ├── sounds.ts                   # ambient/hover/transition sound refs
│   │   ├── cursor.ts                   # which cursor-skin component + behavior
│   │   ├── transitions.ts              # which effects compose for entering/exiting this universe
│   │   ├── narrative.ts                # OPTIONAL — scroll-band structure, only where scroll drives content (see §3a)
│   │   ├── tokens.ts                   # merges the above into one UniverseConfig
│   │   └── config.ts                   # exports `universeConfig`
│   ├── aero-os/                        # About Me            (same shape, narrative.ts optional)
│   ├── cuphead-verse/                  # Projects             (same shape, narrative.ts optional)
│   ├── brutalist-archive/              # Blog                 (same shape, narrative.ts optional)
│   ├── dossier/                        # Resume               (same shape, narrative.ts optional)
│   └── vapor-signal/                   # Contact              (same shape, narrative.ts optional)
│
├── themes/
│   └── seasonal/                       # NOT part of a universe folder — merged on top at resolve time
│       ├── default.ts
│       ├── diwali.ts
│       ├── halloween.ts
│       ├── holi.ts
│       ├── eid.ts
│       └── winter.ts
│
├── legacy/                             # Separate rendering system, not a theme
│   ├── 1998/
│   ├── 2005/
│   ├── 2012/
│   └── plaintext/
│       # each: its own stylesheet + simplified markup, gated by <html data-era>
│
├── components/
│   ├── shell/
│   │   ├── UniverseShell.tsx           # Wraps every route: nav + cursor + navigator
│   │   ├── Navigation.tsx              # Re-skinned per universe via active config
│   │   └── Footer.tsx
│   │
│   ├── cursor/
│   │   ├── CursorLayer.tsx             # Renders the active universe's cursor skin
│   │   ├── CursorEffects.tsx           # Velocity tracking, trails
│   │   └── skins/
│   │       ├── DotCursor.tsx           # Earth-Prime
│   │       ├── BubbleCursor.tsx        # Aero-OS
│   │       ├── HandCursor.tsx          # Cuphead-verse
│   │       ├── PixelCursor.tsx         # Brutalist Archive
│   │       ├── QuillCursor.tsx         # Dossier
│   │       └── GlitchCursor.tsx        # Vapor-Signal
│   │
│   ├── transitions/
│   │   ├── TransitionManager.tsx       # Owns the state machine (see §4)
│   │   ├── TransitionOverlay.tsx       # Full-screen overlay during transit
│   │   └── effects/                    # ★ effect-based, not page-pair-based
│   │       ├── glitch.ts
│   │       ├── collapse.ts
│   │       ├── portal.ts
│   │       ├── paperBurn.ts
│   │       ├── signalWarp.ts
│   │       ├── desktopCrash.ts
│   │       └── archiveRebuild.ts
│   │       # a given universe-pair transition composes 2-3 of these,
│   │       # declared in that universe's transitions.ts, not hardcoded here
│   │
│   ├── multiverse-navigator/
│   │   ├── Navigator.tsx               # The floating device itself
│   │   ├── UniverseMap.tsx             # Expanded map view (backs /universes)
│   │   └── useAutoMinimize.ts          # Hides during immersive moments/idle logic
│   │
│   ├── accessibility/
│   │   ├── AccessibilityPanel.tsx      # The control center UI
│   │   ├── MotionSettings.tsx
│   │   ├── ContrastSettings.tsx
│   │   ├── AudioSettings.tsx
│   │   └── colorblind-filters.tsx      # SVG feColorMatrix defs
│   │
│   ├── command-palette/
│   │   ├── CommandPalette.tsx          # cmdk-based
│   │   └── commands.ts                 # Static command list + dynamic search results
│   │
│   ├── providers/                      # Mounted once in app/layout.tsx
│   │   ├── ThemeProvider.tsx
│   │   ├── UniverseProvider.tsx
│   │   ├── AudioProvider.tsx
│   │   ├── AccessibilityProvider.tsx
│   │   └── SearchProvider.tsx
│   │
│   └── ui/                             # Small shared primitives (Button, Tag, Modal…)
│
├── scenes/                             # All R3F 3D content lives here, isolated from pages
│   ├── shared/                         # Reusable R3F helpers (lighting rigs, post-fx)
│   ├── home/
│   │   ├── FaceMeshScene.tsx
│   │   ├── useFaceMeshScroll.ts        # Scroll → rotation/lighting mapping
│   │   └── useVelocityGlitch.ts        # Cursor velocity → particle dispersion
│   │
│   └── projects/
│       ├── ProjectsRoomScene.tsx
│       ├── objects/
│       │   ├── Mirror.tsx              # Triggers OpenCV camera view on click
│       │   ├── RoboticArm.tsx          # IK-driven, scroll + drag reactive
│       │   ├── Tablet.tsx              # Zooms into ROS2DashboardOverlay
│       │   └── Car.tsx
│       ├── useInverseKinematics.ts
│       └── AccessibleListView.tsx      # Parallel non-3D nav, same data source
│
├── editor/                             # The custom blog CMS, Tiptap-based
│   ├── EditorShell.tsx                 # Three-pane layout (insert / canvas / meta)
│   ├── extensions/
│   │   ├── CodeBlock.ts
│   │   ├── TerminalBlock.ts
│   │   ├── AsciiArtBlock.ts
│   │   ├── StickyNoteBlock.ts
│   │   ├── ThreeDModelBlock.ts
│   │   ├── DiagramBlock.ts
│   │   └── TableBlock.ts
│   ├── sidebar/
│   │   ├── InsertPanel.tsx
│   │   └── MetaPanel.tsx               # TOC, tags, attachments, publish controls
│   └── PostRenderer.tsx                # Reads the same JSON doc, renders read-mode
│                                        # (used by both /blog/[slug] and admin preview)
│
├── stores/                             # Zustand, split by concern — not one giant store
│   ├── universeStore.ts                # currentUniverse, mode
│   ├── themeStore.ts                   # resolved tokens cache
│   ├── audioStore.ts                   # per-category volumes, muted state
│   ├── transitionStore.ts              # transition machine state (see §4)
│   ├── cursorStore.ts                  # position, velocity, hover target
│   ├── accessibilityStore.ts           # motion/contrast/text-size/colorblind prefs
│   └── projectStore.ts                 # active project in Projects room
│
├── lib/
│   ├── theme/
│   │   ├── resolver.ts                 # resolveTheme(universe, mode, season) → merged tokens
│   │   └── types.ts                    # UniverseConfig, ThemeTokens types (see §3)
│   ├── transitions/
│   │   └── machine.ts                  # transition state machine definition (see §4)
│   ├── motion/                         # Centralizes GSAP usage — nothing scattered in components
│   │   ├── timeline.ts
│   │   ├── scroll.ts
│   │   ├── stagger.ts
│   │   └── parallax.ts
│   ├── audio/
│   │   ├── manager.ts                  # Howler wrapper
│   │   └── sound-library.ts
│   ├── analytics/
│   │   ├── events.ts                   # Typed event vocabulary (see §9)
│   │   └── provider.ts                 # Plausible wrapper
│   ├── assets/
│   │   └── manifest.ts                 # Per-universe asset lists, for lazy load/prefetch
│   ├── features/
│   │   └── flags.ts                    # enableAudio, enableFaceMesh, enableUniverseMap, …
│   ├── search/
│   │   └── fuse.ts                     # Fuse.js config for command palette + blog search
│   ├── seasons.ts                      # Date → current season logic
│   ├── network-tier.ts                 # navigator.connection → asset tier
│   ├── db.ts                           # Drizzle client init
│   └── auth.ts                         # Auth.js config
│
├── db/
│   ├── schema.ts                       # Drizzle schema: posts, comments, submissions
│   └── migrations/
│
├── content/
│   ├── projects.json                   # Static project data (or move to DB later)
│   ├── skills.json
│   └── experience.json                 # Sourced from resume — timeline data
│
├── public/
│   ├── assets/
│   │   ├── earth-prime/{light,dark}/{images,glb,audio}
│   │   ├── aero-os/…
│   │   ├── cuphead-verse/…
│   │   ├── brutalist-archive/…
│   │   ├── dossier/…
│   │   └── vapor-signal/…
│   └── fonts/
│
├── styles/
│   └── universes/                      # Per-universe CSS variable sets, imported by resolver
│       ├── earth-prime.css
│       ├── aero-os.css
│       ├── cuphead-verse.css
│       ├── brutalist-archive.css
│       ├── dossier.css
│       └── vapor-signal.css
│
├── types/
│   └── universe.ts                     # Shared UniverseId, Mode, Season types
│
├── tests/
│   ├── unit/
│   ├── e2e/                            # Playwright: transitions, direct nav, reduced-motion
│   └── a11y/                           # jest-axe per-universe checks
│
├── .github/workflows/ci.yml
├── next.config.ts
├── tailwind.config.ts
└── package.json
```

---

## 3. Universe Config + Theme Resolution (the core contract)

Every visual decision traces back to one typed config per universe, plus a seasonal overlay merged on top at resolve time. Nothing hardcodes a universe's colors/fonts/cursor inside a component.

```ts
// types/universe.ts
export type UniverseId =
  | 'earth-prime'       // Home
  | 'aero-os'           // About Me
  | 'cuphead-verse'     // Projects
  | 'brutalist-archive' // Blog
  | 'dossier'           // Resume
  | 'vapor-signal';     // Contact

export type Mode = 'light' | 'dark';
export type Season = 'default' | 'diwali' | 'halloween' | 'holi' | 'eid' | 'winter';

export interface ThemeTokens {
  colors: { background: string; ink: string; accent: string; accentSecondary: string };
  fonts: { display: string; body: string; mono: string };
  invertedColors?: { background: string; ink: string; accent: string; accentSecondary: string };
  // ^ optional — only set where a universe has scroll bands that flip relative to
  // the page's current mode (see §3a). Most universes never set this.
}

export interface UniverseConfig {
  id: UniverseId;
  theme: Record<Mode, ThemeTokens>;
  cursor: { skin: string; trail?: boolean };
  sounds: { ambient: string; hover: string; transition: string };
  // Which effects compose for entering/exiting this universe — see §4/§5
  transitions: { entering: string[]; exiting: string[] };
}
```

Each universe is a self-contained folder, all conforming to this shape:

```ts
// universes/earth-prime/theme.ts
export const theme: UniverseConfig['theme'] = {
  light: { colors: { /* ... */ }, fonts: { /* ... */ } },
  dark:  { colors: { /* ... NEXUS mask palette ... */ }, fonts: { /* ... */ } },
};

// universes/earth-prime/cursor.ts
export const cursor = { skin: 'DotCursor', trail: false };

// universes/earth-prime/sounds.ts
export const sounds = { ambient: 'synth-hum.mp3', hover: 'blip.mp3', transition: 'collapse.mp3' };

// universes/earth-prime/transitions.ts
export const transitions = { entering: ['portal', 'archiveRebuild'], exiting: ['glitch', 'collapse'] };

// universes/earth-prime/config.ts — composes the above into the shape every universe exports
import { theme } from './theme';
import { cursor } from './cursor';
import { sounds } from './sounds';
import { transitions } from './transitions';

export const universeConfig: UniverseConfig = { id: 'earth-prime', theme, cursor, sounds, transitions };
```

Seasonal themes are **not** duplicated per universe — they're small deltas merged on top at resolve time:

```ts
// themes/seasonal/diwali.ts
export const diwaliOverrides: Partial<Record<UniverseId, Partial<ThemeTokens>>> = {
  'earth-prime': { colors: { accent: '#ff9a3c' /* warm diya glow */ } },
  // only universes that actually get a seasonal treatment need an entry here
};
```

```ts
// lib/theme/resolver.ts
import { UNIVERSE_CONFIGS } from './registry'; // imports all 6 universes/*/config.ts
import { SEASONAL_OVERRIDES } from '@/themes/seasonal';

export function resolveTheme(universe: UniverseId, mode: Mode, season: Season): ThemeTokens {
  const base = UNIVERSE_CONFIGS[universe].theme[mode];
  const overrides = SEASONAL_OVERRIDES[season]?.[universe];
  return overrides ? deepMerge(base, overrides) : base;
}
```

Resolved tokens are applied as **CSS custom properties** on a wrapping element (`--color-bg`, `--color-ink`, etc.), so Tailwind utility classes and raw CSS can both consume them without prop-drilling. `stores/themeStore.ts` caches the resolved result; `stores/universeStore.ts` holds `currentUniverse` and `mode`; season is auto-computed from `lib/seasons.ts` but overridable for testing/preview.

Legacy era modes (`legacy/1998`, etc.) sit **outside this entire system** — gated by `<html data-era="1998">`, they load their own stylesheet and a simplified markup path, bypassing `resolveTheme` altogether. They're an alternate rendering system, not a theme variant.

## 3a. Scroll Narrative Bands (optional, per-universe)

Some universes — Home is the first confirmed case — aren't a single flat page but a fixed sequence of visually distinct scroll "bands" (e.g. cream hero → dark face-reveal → cream dreamer/builder → dark constellation → cream universe-cards). The band **order, count, and content never change** between light and dark mode — only which bands render inverted relative to the page's current mode does. This is structural page composition, not a theming concern, so it does **not** live in `theme.ts` or fork by `Mode`.

```ts
// universes/earth-prime/narrative.ts
export interface ScrollBand {
  id: string;
  content: 'hero' | 'face-reveal' | 'dreamer-builder' | 'constellation' | 'universe-cards';
  scene?: string;        // which entry in scenes/{universe}/ mounts here, if any
  inverted?: boolean;    // true = this band always renders using invertedColors,
                         // regardless of the page's current mode
}

export const narrative: ScrollBand[] = [
  { id: 'hero',     content: 'hero' },
  { id: 'reveal',   content: 'face-reveal',    scene: 'FaceMeshScene', inverted: true },
  { id: 'dreamer',  content: 'dreamer-builder' },
  { id: 'stars',    content: 'constellation',  inverted: true },
  { id: 'cards',    content: 'universe-cards' },
];
```

The page component (`app/page.tsx` for Home) maps over this one fixed array regardless of mode:

```tsx
{narrative.map(band => {
  const tokens = band.inverted
    ? resolveTheme('earth-prime', mode, season).invertedColors
    : resolveTheme('earth-prime', mode, season).colors;
  return <ScrollBandRenderer key={band.id} band={band} colors={tokens} />;
})}
```

Toggling light/dark still changes the *design* of every band (per your NEXUS mask alt) — it just doesn't change *which* bands exist or their order, since you've confirmed content and band count are identical across modes. `useFaceMeshScroll` and similar scroll hooks read progress **per band** (via each band's own `ScrollTrigger` instance), not as one continuous progress value across the whole page — this matters because a universe with `narrative.ts` is really N independently-triggered mini-scenes stitched together, not one long animation timeline.

This file is genuinely optional — only add it to a universe once you've confirmed it has real scroll-driven structure (Home does; Contact, for instance, almost certainly doesn't and should stay a normal flat page).

---

## 4. Transition State Machine

Modeled explicitly so direct URL loads, back/forward nav, and full choreographed transitions are all just different entry points into the same states.

```ts
// lib/transition-machine.ts
type TransitionState =
  | { status: 'idle'; universe: UniverseId }
  | { status: 'exiting'; from: UniverseId; to: UniverseId }
  | { status: 'in-transit'; from: UniverseId; to: UniverseId; stage: 1 | 2 | 3 | 4 }
  | { status: 'entering'; universe: UniverseId }
  | { status: 'arrived'; universe: UniverseId };
```

- **Client-side nav (nav bar / navigator jump):** full sequence — `idle → exiting → in-transit(1..4) → entering → arrived`. Plays the tear/glitch/collapse/reform choreography per the storyboard.
- **Direct URL load / refresh / shared link:** skip straight to a **compressed single-stage version** of `entering → arrived` (matches your bookmarking requirement — a fast fade/settle instead of the full 4-stage sequence, so sharing `/projects` doesn't force visitors to sit through an irrelevant "Home" exit animation).
- **`prefers-reduced-motion`:** collapse all `in-transit` stages into an instant cross-fade, still passing through the same states (so route/analytics logic doesn't need separate branches — only the rendered visuals change).

`TransitionOverlay.tsx` subscribes to this state (via `stores/transitionStore.ts`) and, during `in-transit`, composes whichever `components/transitions/effects/*` are declared in the *destination* universe's `transitions.entering` (and the *origin* universe's `transitions.exiting`) — e.g. Home → About plays `glitch` + `collapse` (Earth-Prime's exit) then `portal` + `archiveRebuild` (Aero-OS's entry). This is why effects are named by what they do (`glitch.ts`, `paperBurn.ts`) rather than by page pair — a 6-universe site has up to 30 directed transitions, and composing 7 reusable effects scales far better than hand-authoring 30 bespoke components.

---

## 5. Data Model (Postgres via Drizzle)

```ts
// db/schema.ts
export const posts = pgTable('posts', {
  id: uuid('id').primaryKey().defaultRandom(),
  slug: text('slug').unique().notNull(),
  title: text('title').notNull(),
  content: jsonb('content').notNull(),      // Tiptap JSON doc
  category: text('category'),
  tags: text('tags').array(),
  status: text('status').default('draft'), // draft | published
  publishedAt: timestamp('published_at'),
  readingTime: integer('reading_time'),
  createdAt: timestamp('created_at').defaultNow(),
  updatedAt: timestamp('updated_at').defaultNow(),
});

export const comments = pgTable('comments', {
  id: uuid('id').primaryKey().defaultRandom(),
  postId: uuid('post_id').references(() => posts.id),
  author: text('author'),
  body: text('body'),
  createdAt: timestamp('created_at').defaultNow(),
});

export const contactSubmissions = pgTable('contact_submissions', {
  id: uuid('id').primaryKey().defaultRandom(),
  name: text('name'),
  email: text('email'),
  subject: text('subject'),
  message: text('message'),
  createdAt: timestamp('created_at').defaultNow(),
});
```

Projects/skills/experience stay as **static JSON in `content/`** (sourced from your resume) rather than DB tables — they change rarely and don't need an editor UI; editing the JSON file directly is simpler than building CRUD for it.

---

## 6. Rendering Strategy Per Route

| Route | Strategy | Reasoning |
|---|---|---|
| `/` (Home) | Static (SSG) shell + client-hydrated face mesh | Fast FCP; 3D hydrates after paint |
| `/about` | Static shell, client "OS" interactivity | Windows/desktop icons are DOM, not 3D — cheap to make static |
| `/projects` | Client-heavy (R3F room) behind a lightweight static loading shell | Genuinely needs CSR; show the room's static poster frame first for perceived performance |
| `/blog` | ISR (revalidate on publish via webhook or on-demand revalidation) | Content changes without redeploys |
| `/blog/[slug]` | ISR, `generateStaticParams` for published posts | Good SEO, fast repeat visits |
| `/resume` | Static (SSG) — content sourced from `content/experience.json` | Rarely changes, no reason to hit the DB |
| `/contact` | Static shell + client form (Server Action → Resend) | Form submission doesn't need full CSR page |

---

## 7. Cross-Cutting Systems (apply to every universe identically)

- **`UniverseShell.tsx`** wraps every route: reads current path → resolves `UniverseId` → provides theme via CSS vars → mounts `Navigation`, `CursorLayer`, `Navigator`, `AccessibilityPanel`, `CommandPalette`.
- **`lib/audio/manager.ts`** is a singleton (not per-universe instances) that crossfades ambient tracks on universe change and exposes per-category volume (music/ambience/effects/voice) to the Accessibility Panel, all via `audioStore`.
- **`AccessibilityProvider`** sits above everything, including the transition system — `prefers-reduced-motion` and manual "No Motion" override both flow into `TransitionManager` so reduced-motion isn't a per-component afterthought.
- **Legacy/era modes** (1998/2005/2012/plain-text) live entirely outside this system: a `data-era` attribute on `<html>` swaps in `legacy/{era}/` stylesheets and a simplified markup path, bypassing `resolveTheme` and the modern component tree entirely rather than theming it.

---

## 8. State Stores (Zustand, split by concern)

One store per concern, not one giant app store — each is small enough to reason about in isolation, and components only subscribe to the slice they need (avoids re-rendering the whole app on, say, a cursor position update).

| Store | Holds |
|---|---|
| `universeStore` | `currentUniverse`, `mode` (light/dark) |
| `themeStore` | Cached resolved `ThemeTokens` for the current universe/mode/season |
| `audioStore` | Per-category volumes (music/ambience/effects/voice), muted flag |
| `transitionStore` | The transition state machine's current state (§4) |
| `cursorStore` | Position, velocity, current hover target |
| `accessibilityStore` | Motion/contrast/text-size/colorblind preferences |
| `projectStore` | Which object is active/zoomed-in inside the Projects room |

## 9. Analytics Event Vocabulary

Defined once in `lib/analytics/events.ts` as a typed union, so every call site is type-checked rather than passing raw strings around:

```ts
type AnalyticsEvent =
  | { name: 'universe_switched'; from: UniverseId; to: UniverseId }
  | { name: 'project_opened'; projectId: string }
  | { name: 'blog_post_read'; slug: string; percentScrolled: number }
  | { name: 'transition_completed'; from: UniverseId; to: UniverseId; durationMs: number }
  | { name: 'resume_viewed' }
  | { name: 'contact_submitted' }
  | { name: 'theme_switched'; mode: Mode };
```

`lib/analytics/provider.ts` wraps Plausible's custom-events API; swapping providers later only touches this one file.

## 10. Feature Flags

`lib/features/flags.ts` — a plain object (env-driven or hardcoded per environment), read by components that gate expensive or unfinished features:

```ts
export const FEATURES = {
  enableAudio: true,
  enableFaceMesh: true,
  enableUniverseMap: true,
  enableLegacyModes: false,   // ship after the core 6 universes are stable
  enableOpenCVMirror: false,  // ship after core Projects room interactions work
};
```

This lets you deploy Home before the face mesh is finished, or ship Projects before the OpenCV mirror is ready, without half-built features leaking into production.

## 11. Asset Manifest

`lib/assets/manifest.ts` — one entry per universe, listing what needs to preload/prefetch/lazy-load, keyed the same way as everything else:

```ts
export const ASSET_MANIFEST: Record<UniverseId, { images: string[]; sounds: string[]; models?: string[] }> = {
  'earth-prime': { images: [/* ... */], sounds: [/* ... */], models: ['/assets/earth-prime/face-mesh.glb'] },
  // ...
};
```

Used by `lib/network-tier.ts` to decide what to actually fetch based on `navigator.connection.effectiveType` (your "Lite Experience Recommended" requirement).

---

## 12. Suggested Build Order (maps to this structure — do not reorder without a real reason)

1. `types/universe.ts` + one universe folder (`universes/earth-prime/*`) as the template + `lib/theme/resolver.ts` + `stores/universeStore.ts`/`themeStore.ts` — nothing else can start meaningfully before this exists.
2. `components/shell/*`, `cursor/*`, `transitions/*` (with 2-3 placeholder effects) — prove the shared skeleton works across at least two real universe configs (Home + one other, e.g. Aero-OS).
3. `app/page.tsx` (Home) fully built — first real universe, validates the whole pipeline including `scenes/home/FaceMeshScene.tsx` **and** `universes/earth-prime/narrative.ts` (§3a), since Home is the confirmed case of scroll-band structure. This is also the moment to prove out per-band `ScrollTrigger` instances rather than one continuous scroll timeline.
4. `app/projects/page.tsx` + `scenes/projects/*` — highest technical risk (IK, OpenCV worker), tackle early while there's still time to descope if needed.
5. `editor/*` + `db/schema.ts` + `app/admin/*` — decoupled enough from the universe shell to build in parallel.
6. Remaining 4 universes (About, Blog, Resume, Contact) — lower risk once the shell and per-universe config pattern are proven on two real examples.
7. `themes/seasonal/*`, `legacy/*`, `lib/analytics/*`, `lib/features/flags.ts`, `components/accessibility/*`, `components/command-palette/*` — layered on last, tested against every universe as each is added, not bolted on only at the very end.
