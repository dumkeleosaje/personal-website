# AGENTS.md — Dumkele's personal site ("Signal")

Read this file fully before every task. It is the source of truth for design and code decisions.
If a request conflicts with this file, point out the conflict and ask before proceeding.

---

## 1. The concept: "Signal from Noise"

The site is presented like a research publication crossed with a financial broadsheet. The idea behind it:
my work (CS, AI, ML, quantitative thinking, computational biology) is about finding structure in noisy data.

- The homepage opens on a field of scattered marks (noise) that **resolves into structure** (my name and a clean signal line) as the visitor arrives or scrolls.
- Projects are not "cards". They are **Figures**: `Fig. 01`, `Fig. 02`… each with a caption, a small **generative plot unique to that project**, and annotated metadata.
- The about section is called **Method**, the experience section is called **Record** (styled as a ledger), and the contact section is called **Correspondence**.
- Tone: precise, quietly confident, a little witty in the metadata. Never salesy.

## 2. Stack (do not change without asking)

- Astro (latest stable) + TypeScript (strict)
- Tailwind CSS (latest stable) for utilities; design tokens live as CSS custom properties
- Projects: Astro content collections in `src/content/projects/*.mdx` with a typed Zod schema
- Interactive pieces: vanilla TS or small Astro islands. **No React/Vue unless I approve it.**
- Canvas / SVG for generative visuals. No heavy animation libraries (no GSAP, no Three.js) unless approved.
- Package manager: npm. Target host: Vercel (static output).

## 3. Design system

### Colour: light theme "Blush Paper" (default)
| Token            | Value     | Use                                   |
|------------------|-----------|---------------------------------------|
| `--paper`        | `#F4E8E4` | page background (pale blush, not candy pink) |
| `--paper-raised` | `#FBF3F0` | subtle raised surfaces                |
| `--ink`          | `#1B1416` | primary text                          |
| `--ink-muted`    | `#6B5A5E` | secondary text, captions              |
| `--rule`         | `#D9C6C1` | hairlines, grid lines                 |
| `--signal`       | `#E0442B` | the ONE accent: signal line, links, focus |
| `--cobalt`       | `#2340D8` | data-only secondary (plots), never for UI chrome |

### Colour: dark theme "Darkroom"
| Token            | Value     |
|------------------|-----------|
| `--paper`        | `#120D0F` |
| `--paper-raised` | `#1C1518` |
| `--ink`          | `#F4E8E4` |
| `--ink-muted`    | `#A8969A` |
| `--rule`         | `#33282C` |
| `--signal`       | `#FF5A3C` |
| `--cobalt`       | `#7C8CFF` |

- Respect `prefers-color-scheme`, plus a manual toggle that persists in `localStorage` (wrapped in try/catch). No flash of the wrong theme on load.
- The accent is used sparingly. If more than ~5% of a screen is `--signal`, it's too much.
- All text must meet WCAG AA contrast in both themes.

### Typography (Google Fonts or Fontsource, self-hosted)
- Display: **Instrument Serif** (big, tight leading, used for headings and figure titles)
- Body: **Geist** (or IBM Plex Sans if Geist is unavailable)
- Mono: **Geist Mono** (or IBM Plex Mono). Used for metadata, labels, figure numbers, the status line
- Fluid type scale using `clamp()`. Display headings can be very large (up to ~9rem on desktop).
- Body text max width ~68ch. Line-height 1.55 for body, 0.95–1.05 for display.

### Layout
- 12-column broadsheet grid with visible hairline rules in places (column rules, section rules), like a printed paper.
- Asymmetric compositions: headings may hang left in the margin, captions sit in narrow columns.
- Marginalia: on desktop, small mono annotations in the margins (e.g. `n = 4 projects`, `est. 2006 → ∞`). On mobile they fold inline.
- Spacing scale based on 4px. Generous vertical rhythm between sections.
- Mobile-first, flawless from 360px wide. No horizontal scroll ever.

### Motion
- Purposeful only: the noise→signal resolve, figure plots drawing in, subtle hover states.
- Durations 150–600ms, easing `cubic-bezier(0.2, 0.8, 0.2, 1)`.
- Everything must respect `prefers-reduced-motion` (show the resolved final state instantly).
- Canvas animations pause when off-screen (IntersectionObserver) and when the tab is hidden.

## 4. Banned — these make sites look AI-generated
- Purple/blue gradients, glassmorphism, glowing blobs, neon on black, aurora backgrounds
- Emoji in headings or nav; "Hi, I'm X 👋"; "passionate developer"; "turning ideas into reality"
- Centred hero + three identical feature cards; generic bento grids
- Skill bars, percentage meters, logo clouds of every technology
- Typewriter text effects, cursor-follower blobs, parallax for its own sake
- Stock icons used decoratively; lorem ipsum
- **Inventing any personal content**: projects, metrics, employers, dates, quotes. If content is missing, use a clearly marked `TODO:` placeholder and tell me.

## 5. Site structure
```
/                 Index: noise→signal hero, status line, selected Figures, Method teaser, Record, Correspondence
/figures          All projects as a numbered index (filterable by tag)
/figures/[slug]   Individual project write-up (MDX), with its generative plot as the header
/404              Styled as "Fig. 404 — no signal detected"
```

### Project (Figure) content schema
```ts
title: string
figure: number            // display order, renders as "Fig. 01"
summary: string           // one-sentence caption
role: string
stack: string[]
year: number
status: "shipped" | "ongoing" | "research"
metrics?: { label: string; value: string }[]   // real numbers only
links?: { label: string; href: string }[]
tags: string[]
seed: number              // drives that project's generative plot
featured: boolean
draft: boolean
```

## 6. Quality bar
- `npm run build` passes with zero errors and zero TypeScript errors.
- Lighthouse 95+ in Performance, Accessibility, Best Practices, SEO.
- Semantic HTML, a single `h1` per page, visible focus styles in `--signal`, full keyboard navigation, skip link.
- Images via Astro's image tooling, with width/height set (no layout shift).
- Per-page `<title>`, meta description, Open Graph and Twitter tags, canonical URL, sitemap, robots.txt.
- Keep JS shipped to the client minimal. Prefer CSS where possible.

## 7. How to work
- One focused change per task. At the end, summarise what changed and which files.
- Don't add dependencies without saying why.
- Don't rewrite or "improve" anything in `src/content/` copy unless I ask.
- Run `npm run build` before saying a task is done.
- Use the `$frontend-design` skill for any visual or UI work.
