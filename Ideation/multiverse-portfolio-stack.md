# Multiverse Portfolio — Full Technical Stack

## 1. Core Framework

| Piece | Choice | Why |
|---|---|---|
| Framework | **Next.js 15 (App Router)** | Route-per-universe maps cleanly to `/`, `/about`, `/projects`, `/blog`, `/resume`, `/contact`. Server Components for static universe content; Client Components for interactive/3D. Built-in route-level code splitting (you already specced this as a requirement). |
| Language | **TypeScript**, strict mode | 6 universes × light/dark × mobile/desktop = a lot of config surface. Typed theme tokens will save you from silent bugs. |
| Package manager | **pnpm** | Faster installs, better monorepo support if you split the blog editor into its own package later. |
| Rendering | Hybrid: SSG for Home/About/Resume static shells, ISR for Blog posts, CSR-heavy for Projects (3D room) | Keeps Lighthouse performance high on pages that don't need JS-heavy hydration immediately. |

## 2. 3D / WebGL

| Piece | Choice | Why |
|---|---|---|
| 3D engine | **Three.js** via **React Three Fiber (R3F)** | You already specced this for the face mesh and Projects room. |
| Helpers | **@react-three/drei** | Cheap wins: `OrbitControls`-style rig (build custom, since native OrbitControls is disabled per your r128 pin — actually recommend r150+ instead, see note below), `useGLTF`, `Html` for DOM-in-3D labels, `Environment` for lighting. |
| Face mesh | **Face mesh generated via a 3D scan/photogrammetry pipeline** (e.g. one-time offline process using a service like Luma AI or a simple depth-photo rig) exported as `.glb`, then driven at runtime by scroll position + `useFrame` | Real-time face reconstruction in-browser is not practical; bake the mesh once, animate it live. |
| Physics/IK | **three-ik** or a hand-rolled FABRIK solver | For the robotic arm's inverse kinematics — three-ik is small and framework-agnostic, plugs into R3F easily. |
| Particle effects | **@react-three/drei**'s `Points` + custom shaders, or **tsParticles** for 2D transition particles | Wireframe-dispersing-into-particles effect on the face mesh is a shader/GPU-instancing job — hand-write a vertex shader that offsets vertices by noise × velocity. |
| Asset pipeline | **glTF/GLB** everywhere, compressed with **Draco** or **Meshopt** | Matches your "texture compression, asset streaming" requirement. |

> Note: your notes reference Three.js r128 constraints — that's an artifact-preview sandbox limitation, not a real production constraint. For the actual site, use current Three.js (r165+) and real `OrbitControls`/`DragControls` from three/examples — no need to avoid `CapsuleGeometry` etc.

## 3. Animation & Motion

| Piece | Choice | Why |
|---|---|---|
| Scroll-driven animation | **GSAP + ScrollTrigger** | Industry standard for exactly what you're describing (Home page "turns as you scroll," face mesh rotation, resume paper unfolding). More reliable and performant than Framer Motion for scroll-linked scrubbing. |
| Component/page transitions | **Framer Motion (motion/react)** | Best React-native fit for the "screen tears apart" universe transitions — `AnimatePresence` handles exit/enter choreography cleanly. |
| Page-transition orchestration | Custom **transition state machine** (XState or a hand-rolled reducer) | You already have this practically pre-written from the mobile transition storyboard (tap → glitch → collapse → reform → new universe). Model it as explicit states so it's testable and so direct-URL loads can jump straight to the "arrived" state (your bookmarking requirement). |
| Physics-y feel (optional) | **Framer Motion's built-in spring physics** or **react-spring** | For sticky notes fluttering, plants swaying — small ambient physics, not full rigid-body sim. |

## 4. Styling & Theming

| Piece | Choice | Why |
|---|---|---|
| Styling | **Tailwind CSS v4** | Fast to theme per-universe with CSS variables; works cleanly with the frontend-design constraints you'll want per universe. |
| Theme tokens | Custom **design token system**: one JSON/TS object per universe × mode (bg, ink, accent, cursor asset, font pair, sound set) | This is the single most important artifact to lock before writing UI code — I can draft this next if useful. |
| Fonts | **next/font** with per-universe pairs (e.g. a geometric sans for Home, a monospace for Blog's brutalist mode, a serif/typewriter for Resume, a display glitch font for Contact) | Self-hosted via next/font avoids FOUT and keeps Lighthouse happy. |
| CSS-in-JS avoidance | Prefer Tailwind + CSS variables over styled-components/emotion | Runtime CSS-in-JS hurts the performance budget you set (Lighthouse >90). |

## 5. Custom Cursor & Interaction Layer

| Piece | Choice | Why |
|---|---|---|
| Cursor engine | A single **`<CursorProvider>`** context reading current universe + hover target, rendering an absolutely-positioned DOM element (not actual `cursor: url()`, for smooth animation) | One system, six skins — matches your "shared cursor philosophy" principle. |
| Velocity tracking | Custom hook sampling `pointermove` deltas over time (for face-mesh glitch-on-rapid-movement) | Lightweight, no library needed. |
| Respect `prefers-reduced-motion` | Build this into the cursor/transition/parallax systems at the hook level from day one, not retrofitted | You've already specced this as a hard requirement — cheapest to do correctly from the start. |

## 6. Audio

| Piece | Choice | Why |
|---|---|---|
| Audio engine | **Howler.js** | Handles cross-browser audio sprites, per-category volume (music/ambience/effects/voice — your accessibility spec), fade transitions between universes. |
| Procedural/reactive audio (Home synth hum reacting to scroll) | **Tone.js** | Purpose-built for generative/reactive audio synthesis in the browser; overkill for simple SFX but right tool for a scroll-reactive synth pad. |
| Audio descriptions | Plain `<track kind="descriptions">` or a custom transcript panel toggled from the Accessibility Control Center | Matches your WCAG AA target. |

## 7. Computer Vision (Projects — mirror/webcam filters)

| Piece | Choice | Why |
|---|---|---|
| CV | **OpenCV.js** (WASM build) run in a **Web Worker** | Keeps the camera filter processing off the main thread so it doesn't jank the 3D room. |
| Camera access | `getUserMedia` gated behind an explicit user-initiated click (never auto-start) | Required for both UX and privacy/permissions reasons. |
| Fallback | Face landmark filters could alternatively use **MediaPipe Tasks (Face Landmarker)** which is lighter than full OpenCV for simple filters | Consider this if the goal is Snapchat-style filters rather than raw CV image processing. |

## 8. Blog: Custom Block Editor (CMS)

This is a real product in itself. Recommended approach:

| Piece | Choice | Why |
|---|---|---|
| Editor core | **Tiptap** (built on ProseMirror) with custom **Node extensions** per block type (code, terminal, ASCII art, sticky note, 3D model embed, table, diagram) | This is the standard foundation for exactly this kind of block-based, draggable, mixed-media editor — don't build a ProseMirror wrapper from scratch. |
| Drag-and-drop block reordering | **dnd-kit** | Accessible (keyboard-operable, matches your a11y requirements), performant, framework-native to React. |
| Autosave / versioning | Debounced writes to your backend + a simple version-snapshot table | Doesn't need a fancy CRDT system unless you want real-time multi-device sync — you're the only author. |
| Content storage | Store each post as **structured JSON** (Tiptap's native JSON doc format), not raw HTML | Lets you render the same content two ways: brutalist archive card view AND the ASCII-art expanded reader view, without re-parsing HTML. |
| 3D/diagram embeds in posts | Custom Tiptap node that renders an R3F `<Canvas>` or a Mermaid diagram inline | Reuses your existing R3F setup. |

## 9. Backend / Data Layer

| Piece | Choice | Why |
|---|---|---|
| Database | **PostgreSQL** (e.g. via **Supabase** or **Neon**) | Blog posts (JSON content), tags, comments if you keep those, contact form submissions. |
| ORM | **Drizzle ORM** or **Prisma** | Drizzle if you want lighter weight and closer-to-SQL control; Prisma if you want more DX polish. |
| API layer | **Next.js Route Handlers** (`app/api/*`) | No need for a separate backend service at this scale. |
| Contact form delivery | **Resend** or **Nodemailer + SMTP** | Resend is the simpler, more modern choice for a personal-site contact form. |
| Auth (for your own blog admin) | **NextAuth.js / Auth.js**, single-admin gate | You're the only author — a simple password/passkey gate is enough, no need for full multi-user auth. |
| File/image storage | **Vercel Blob** or **Supabase Storage** | For blog images/videos/3D model uploads from the editor. |

## 10. ROS2 Command Center (the "secret" dashboard easter egg)

You don't need a live ROS2 bridge for a portfolio piece — that's real infrastructure risk for a website. Recommended:

- **Option A (recommended): pre-recorded/simulated telemetry.** Store a JSON timeline of realistic telemetry (battery %, node status, a fake rover path) and replay it client-side on a loop with WebSocket-like fake push via `setInterval`. Looks and feels live, zero infra dependency, zero uptime risk.
- **Option B (if you want it real): rosbridge_suite + roslibjs** connecting to an actual ROS2 instance you run — only worth it if you want to literally teleop a real rover from the website, which is a fun stretch goal but a separate infra project (needs a publicly reachable rosbridge websocket, security hardening, and a robot that's actually on and available).

Start with A. Upgrade to B later if you want the flex.

## 11. Performance & Asset Optimization

| Piece | Choice | Why |
|---|---|---|
| Image formats | **AVIF → WebP → fallback**, via `next/image` | Matches your spec directly. |
| 3D asset compression | **Draco/Meshopt** compression, **KTX2** texture compression | Standard glTF pipeline optimization. |
| Route-level splitting | Next.js App Router does this by default per route/universe | Free win from framework choice. |
| Network-aware loading | `navigator.connection.effectiveType` check → conditional "Lite Experience" banner and asset swap | You already specced this exactly — implement as a top-level context that gates which asset tier loads. |
| Monitoring | **Vercel Analytics** + **Lighthouse CI** in your GitHub Actions pipeline | Keeps you honest against the >90 Performance / >95 Accessibility targets over time, not just at launch. |

## 12. Accessibility Tooling

| Piece | Choice | Why |
|---|---|---|
| Testing | **axe-core** (via `@axe-core/react` in dev, `jest-axe` in tests, Lighthouse CI in prod pipeline) | Catches regressions automatically rather than manual re-checking every universe. |
| Reduced motion / high contrast | CSS `@media (prefers-reduced-motion)` / `(prefers-contrast)` **plus** an explicit override in your Accessibility Control Center (stored in a cookie/localStorage-equivalent — actually just React Context + `localStorage`, this is a normal site not an artifact) | Both system-level and manual override, as you specced. |
| Screen reader alt navigation | A parallel **List View** component for the Projects 3D room, built from the same data source that populates the 3D objects | Single source of truth: one array of `{id, name, description, position}` drives both the 3D scene and the accessible list. |
| Colorblind modes | CSS filter-based SVG color matrices (`feColorMatrix`) applied via a root-level `<svg>` filter defs + CSS `filter: url(#protanopia)` | Cheap, GPU-accelerated, no need for per-component recoloring. |

## 13. Internationalization Readiness

| Piece | Choice | Why |
|---|---|---|
| i18n | **next-intl** | Since you noted "design for future translations" — wrap all copy in translation keys from day one even if you only ship English first. Retrofitting i18n after 6 hardcoded-string universes is much more painful than starting clean. |

## 14. Seasonal / Legacy Modes

| Piece | Choice | Why |
|---|---|---|
| Seasonal theming | Server-computed "current season" (date-based) feeding into the same design-token system as light/dark — treat season as a **third theme axis**, not a special case | Diwali/Halloween/Holi/Eid/Winter become just another value in your token resolver: `getTheme(universe, mode, season)`. |
| Legacy browser modes (1998/2005/2012/dark/plain-text) | A **separate, drastically simpler CSS/HTML stylesheet set** per era, toggled via a `<html data-era="1998">` attribute that swaps stylesheets entirely | Don't try to reuse the modern component tree for the 1998 mode — genuinely render simpler markup (table layouts, `<marquee>`-style jokes, etc.) behind a feature flag. This is also your real fallback for very old/JS-disabled browsers. |

## 15. Command Palette

| Piece | Choice | Why |
|---|---|---|
| Cmd+K | **kbar** or **cmdk** | Both are built exactly for this (search projects/skills/blog/experience), accessible by default, easy to theme per-universe. |

## 16. Testing

| Piece | Choice | Why |
|---|---|---|
| Unit/component | **Vitest + React Testing Library** | Fast, ESM-native, standard Next.js pairing. |
| E2E | **Playwright** | Test the transition state machine end-to-end, direct-URL navigation (your bookmarking requirement), and reduced-motion behavior. |
| Visual regression | **Playwright's screenshot testing** or **Chromatic** if you want it | Six visually distinct universes are exactly where visual regression testing earns its keep. |

## 17. Deployment & Infra

| Piece | Choice | Why |
|---|---|---|
| Hosting | **Vercel** | Native Next.js support, edge caching, image optimization, analytics — matches everything above with zero extra config. |
| Database hosting | **Supabase** or **Neon** (serverless Postgres) | Pairs naturally with Vercel's serverless model. |
| CI/CD | **GitHub Actions**: lint → typecheck → unit tests → Lighthouse CI → Playwright → deploy preview | Catches regressions on every PR, especially for accessibility and performance since those are explicit targets, not afterthoughts. |
| Error monitoring | **Sentry** | Especially valuable given how much client-side interactivity (3D, CV, audio) is prone to runtime errors on odd devices. |

## 18. Suggested Repo Structure

```
/app
  /(universes)
    /page.tsx                → Home
    /about/page.tsx
    /projects/page.tsx
    /blog/page.tsx
    /blog/[slug]/page.tsx
    /resume/page.tsx
    /contact/page.tsx
  /api
    /contact/route.ts
    /blog/route.ts
/components
  /cursor
  /transitions
  /accessibility-panel
  /multiverse-navigator
  /command-palette
/lib
  /theme-tokens.ts           → the single source of truth: universe × mode × season
  /transition-machine.ts
  /audio-manager.ts
/scenes                       → R3F scene components
  /face-mesh
  /projects-room
/editor                       → the custom blog CMS (Tiptap-based)
/content                      → seed data, blog JSON docs (or DB-backed)
/public
  /assets/{universe}/{mode}/  → images, glb, audio per universe/mode
```

---

### Recommended build order (not just a stack list)

1. **Design tokens + theme resolver** (`lib/theme-tokens.ts`) — everything else reads from this.
2. **Navigation shell + custom cursor + transition state machine** — the shared skeleton, built once.
3. **One fully-realized universe** (suggest Home, since it's the entry point) to prove the pattern end-to-end.
4. **Projects (3D room)** next, since it's technically the riskiest piece (R3F + IK + OpenCV) — better to find out early if anything here needs to be descoped.
5. **Blog + custom editor** — the biggest scope item after Projects; can be built in parallel by treating it as its own mini-app.
6. **Resume, About, Contact** — lower technical risk, mostly animation/content work once the shell exists.
7. **Accessibility control center + command palette + legacy modes** — layer on last, but test against every universe as you go, not only at the end.
