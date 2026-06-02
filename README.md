# Frontend Design System

A teal-on-white design system built with Claude Code's `frontend-design` skill — featuring 20+ modern UI effects, a full component library, and an interactive effects showcase.

---

## What's Inside

```
front-end-design/
├── html/
│   ├── design-system.css     # Shared design tokens & component styles
│   ├── demo.html             # Full component library (all sections)
│   ├── demo2.html            # Interactive effects showcase (bento grid)
│   ├── frontend-demo.html    # First-pass demo (original aesthetic)
│   └── sample-preview.html   # Style exploration sample
├── skills/
│   └── frontend-design/
│       └── SKILL.md          # Copy of the Claude Code skill
└── README.md
```

---

## The Skill — `frontend-design`

This project was built using the official **`frontend-design`** Claude Code plugin. The skill instructs Claude to create distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics.

### What the skill does

- Commits to a **bold aesthetic direction** before writing a single line of code (brutalist, editorial, luxury, retro-futuristic, etc.)
- Chooses **distinctive typography** — never Inter, Arial, or system fonts
- Applies **real motion design**: scroll-triggered reveals, spring physics, CSS `@property` animations
- Uses **atmospheric backgrounds**: gradient meshes, noise textures, particle networks, aurora sweeps
- Writes **production-grade code** — not throwaway prototypes

The `SKILL.md` file in `/skills/frontend-design/` contains the full prompt that drives the skill.

### Install the plugin in Claude Code

```bash
/plugin install frontend-design
```

Then reload:

```bash
/reload-plugins
```

Once installed, trigger it with:

```bash
/frontend-design Build me a pricing page with a dark editorial aesthetic
```

Or it activates automatically when you ask Claude to build UI components, pages, or applications.

> **Plugin source:** `claude-plugins-official` marketplace  
> **Install path:** `~/.claude/plugins/cache/claude-plugins-official/frontend-design/`

---

## HTML Files

### `demo.html` — Full Component Library

The complete design system demo. Every component lives inside a **section card** with a running border, teal corner glow, and noise texture background. Open directly in any browser — no build step, no dependencies beyond Google Fonts.

**Sections:**
| Section | Components |
|---|---|
| Hero | Typewriter heading, animated counters, breathing glow orb |
| Typography | 7 type scale rows, each with a unique hover effect |
| Colors | 12 token swatches with lift + glow on hover |
| Buttons | Primary, secondary, ghost, dark, danger — 3 sizes, icon variants |
| Forms | Inputs, select, textarea, checkboxes, radio, toggles, range sliders |
| Cards | Running border, lift & glow, glassmorphism, stat cards |
| Badges & Tags | 6 badge variants, dismissible tags |
| Avatars | Stacked group with spring pop on hover |
| Progress Bars | 4 gradient variants, shimmer animation, scroll-triggered fill |
| Alerts | Info, success, warning, danger — dismissible |
| Table | Sortable columns, live text filter |
| Tabs | 4-panel with fade-up transition |
| Modal | Standard + confirm dialog with spring entrance |
| Tooltips | CSS-only hover reveal with up-fade |
| Skeleton | Wave-shimmer loading state |
| Code Block | Syntax highlighted with copy button |
| Timeline | Dot glow on hover, gradient line |
| Pricing | 3-tier with featured gradient card |
| Features | 6-item grid with icon spring bounce |

**Effects in `demo.html`:**
- Aurora sweep (rotating conic-gradient, full page)
- Particle network canvas (cursor attraction + connection lines)
- Cursor spotlight (radial glow follows mouse)
- Custom cursor (dot + lagging ring)
- 3D card tilt (18° perspective + specular highlight)
- Magnetic buttons (physical drift toward cursor)
- Click ripple (material-style expanding ring)
- Running border (conic-gradient orbits on hover)
- Scroll-triggered reveals (staggered fade-up)
- Animated counters (cubic-bezier eased number animation)
- Per-row typography effects (scramble, gradient fill, highlight sweep, word stagger, blur-in, typewriter)

---

### `demo2.html` — Effects Showcase

A bento-grid layout where each card demonstrates one specific effect in isolation. Hover any card to see the effect fire and reveal a subtle label explaining what technique powers it.

**24 effects on display:**

| Effect | Technique |
|---|---|
| Running Border | CSS `@property` + `conic-gradient` angle animation |
| 3D Card Tilt | `rotateX/Y` driven by mouse position, specular radial gradient |
| Magnetic Pull | Cursor offset → `translate()`, spring release |
| Cursor Spotlight | `radial-gradient` at CSS custom property `--mx/--my` |
| Aurora Sweep | `@property` angle on full-page `conic-gradient` |
| Particle Network | Canvas API, cursor attraction, connection lines |
| Glassmorphism | `backdrop-filter: blur` + translucent tinted surface |
| Lift & Shadow Bloom | `translateY` + `scale` with spring cubic-bezier |
| Shimmer Sweep | Pseudo-element slides via `@keyframes` |
| Text Scramble | JS ticks random chars → resolves to final string |
| Gradient Text Fill | `background-clip: text` + shifting `background-position` |
| Highlight Sweep | Pseudo-element `scaleX(0→1)` under text |
| Word Stagger | Split into spans, staggered `transition-delay` |
| Blur-In Reveal | `filter: blur(8px→0)` + `opacity: 0→1` |
| Typewriter | Character-by-character append with random delay |
| Animated Counter | `IntersectionObserver` + cubic-bezier easing |
| Click Ripple | Injected span, `scale(0→4)` keyframe |
| Scroll Reveal | `IntersectionObserver` + staggered class toggle |
| Skeleton Wave | `background-position` animation on gradient |
| Badge Pulse | `box-shadow` keyframe loop |
| Toggle Glow | `box-shadow` on active state |
| Avatar Pop | Spring `translateY` + `scale` on hover |
| Custom Cursor | Dot: instant. Ring: linear interpolation each `rAF` |
| Progress Animate | Width from 0 on hover, shimmer keyframe loops |

---

### `design-system.css` — Shared Token File

The single CSS file that both demos link to. Import it in any HTML file to get the full token set and component styles.

**Token categories:**
```css
--teal / --teal-dark / --teal-deep / --teal-light / --teal-pale / --teal-mist
--text / --text2 / --text3
--border / --border2
--success / --danger / --warn / --info  (+ -bg variants)
--radius / --radius-lg / --radius-xl
--sans / --display / --mono
```

**Component classes included:** `.btn`, `.input`, `.card`, `.card-outer`, `.card-plain`, `.card-glass`, `.badge`, `.tag`, `.avatar`, `.alert`, `.toggle`, `.tab-btn`, `.modal`, `.tip`, `.toast`, `.skeleton`, `.progress-bar`, `.feat-item`, `.pricing-card`, `.timeline`, and more.

---

### `frontend-demo.html` — Original Demo

The first full component demo, built with the warm amber/cream editorial aesthetic (Cormorant Garamond + IBM Plex). Includes light/dark mode toggle. Kept as a reference for a different aesthetic direction.

---

### `sample-preview.html` — Style Sample

A focused 3-card hero used to iterate on the teal color palette and hover effects before building the full demo.

---

## Tech

- **Zero build step** — pure HTML, CSS, and vanilla JS
- **No frameworks** — works by opening the file in a browser
- **Google Fonts** — Bricolage Grotesque (display) + Plus Jakarta Sans (body)
- **CSS `@property`** — used for animating `conic-gradient` angles (running border, aurora)
- **Canvas API** — particle network with cursor attraction
- **IntersectionObserver** — scroll-triggered counters, progress bars, reveals

---

## Usage

Clone or download, then open any HTML file directly:

```bash
open html/demo.html
open html/demo2.html
```

To use the design tokens in your own project, link the CSS:

```html
<link rel="stylesheet" href="html/design-system.css">
```

---

## Built With

[Claude Code](https://claude.ai/code) + the `frontend-design` skill from the official plugin marketplace.
