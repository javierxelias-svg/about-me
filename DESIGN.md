# DESIGN.md: the look of this page

Source: my own "Day One" design (Oct 2026). Soft aura bento dashboard: a dark canvas, glowing gradient tiles, dot-matrix numbers, rounded glass chips. **Build to this; don't invent a different style.** Don't make it a full Apple liquid-glass look, and use no rainbow or chromatic edges.

## Fonts (self-hosted in /fonts, OFL licensed, no CDN)
- UI text: **Geist** (fonts/geist-latin.woff2 + geist-latin-ext.woff2, variable 300–500). Body is 300 weight, letter-spacing -0.01em.
- Numbers: **Doto** dot-matrix (fonts/doto-latin.woff2 + doto-latin-ext.woff2, 600/800), `font-variant-numeric: tabular-nums`.
- Use `@font-face` with `font-display: swap` and a system fallback: `ui-sans-serif, system-ui, -apple-system, sans-serif` and `ui-monospace, monospace`.
- latin-ext is required: the page contains Ā and Ē (ĀRIAxĒRON).

## Color tokens (copy exactly)
Light:
`--canvas:#E4E4E2; --tile:#F1F1EF; --paper:#FAFAF9; --ink:#1B1C1E; --soft:#8D8F92; --faint:#C2C4C6; --lime:#DDF53C; --rose:#F0397F; --peach:#FFB27A; --sky:#8DB0F0; --violet:#B48CF2; --leaf:#3DBB4C; --tang:#FF8A1F; --g-base:#CFDDBD; --o-base:#DCDCE2; --p-base:#F3D9E4;`
Dark (`prefers-color-scheme: dark`):
`--canvas:#0C0D0F; --tile:#141619; --paper:#1A1D21; --ink:#ECEDEF; --soft:#8A9097; --faint:#343940; --rose:#FF4A92; --peach:#FF9E5E; --sky:#6F9BFF; --violet:#A27BFF; --leaf:#34D35A; --tang:#FF7A0F; --g-base:#0F2414; --o-base:#22150C; --p-base:#2A0F1C;`
Text on any tile must still meet a 4.5:1 contrast ratio.

## Shapes and depth
- Tile radius 34px. Chips and pills radius 999px.
- Tile shadow `0 10px 30px rgba(0,0,0,.035)` (light) / `rgba(0,0,0,.4)` (dark). Top rim `inset 0 1px 0 rgba(255,255,255,.9)` light / `.06` dark.
- Optional fine film grain: an inline SVG `feTurbulence` data URI at low opacity (.10–.16).

## Layout: 12-column bento, gap 16px, max-width 1200px
1. **Hero** (full width): small label "About me · Javier Elías Crespo"; name as h1 (Geist 400, clamp(42px, 6.2vw, 76px), letter-spacing -0.05em); the hook line under it at a larger size than body.
   **Stat row** in Doto (clamp(40px, 5vw, 58px)), each number with a small pill label: **341** boards · **16,129** listings · **158 of 159** cloud runs. A lime "Live" chip with a pinging dot next to CareerOS.
2. **Project tiles, one aura color each** (blurred radial-gradient glow behind a tile, `::before`, `filter: blur(26px)`, slow "breathe" scale animation):
   - CareerOS = green aura (leaf / g-base). Big Doto number: **158 of 159** "cloud runs succeeded".
   - XPT OS = orange aura (tang / o-base). Big Doto: **41 → 4** "emails sent → replies".
   - Food Pantry Weekly Digest = pink aura (rose / p-base). A sonar-ping dot and "open it".
   - ĀRIAxĒRON = sky/violet glass orb. Big Doto: **61** "applicants in a month".
   Each tile is a `<details>`. The summary shows the title, the big number and one line, so the page reads without clicking. Opening it shows the full sentence from content.md plus the proof link.
3. **Who I am** tile (paper background, plain text).
4. **How I work** tile: "Verification over vibes." in larger type.
5. **Reach me**: pill buttons (paper chips) for email, LinkedIn, GitHub, Portfolio.

Breakpoints: below 960px tiles span 6 of 12; below 600px they go full width. No sideways scroll at 320px.

## Motion (CSS only, no JavaScript)
- `ping` (status dots), `breathe` (aura glows, 8s), a gentle `bob` on one floating chip at most.
- Wrap everything in `@media (prefers-reduced-motion: reduce) { animation: none }`.
- No count-up numbers (that needs JS). No theme toggle (the OS setting decides).

## Contrast fixes (these override the token list above)
- --soft (#8D8F92) is for decoration only (dots, rules, ticks). It fails as text: 3.1:1 on paper, 2.55:1 on canvas.
- Secondary TEXT uses --soft-text: #5E6166 in light (5.95:1 on paper, 4.88:1 on canvas) and #9AA0A7 in dark.
- All text on an aura tile is --ink in light mode (6.8:1 on leaf, 7.2:1 on tang, 4.5:1 on rose). No white text on aura in light mode.
- Dark mode: text on an aura tile sits on a centered scrim, rgba(12,13,15,.45) with radius 20px. Or keep the aura's bright core out from behind the text. White on a bright core fails (1.98:1 on #34D35A).
- The page starts in LIGHT mode ("dark canvas" in the intro refers to dark mode only).

## Type scale (Geist unless noted). Only these sizes.
- 13px label/pill · 16px tile title · 19px body (300) · 22–24px hook (300, max 34ch) · 28px section h2 (400, -0.03em)
- Doto: hero stats clamp(40px,5vw,58px) 600 · tile hero number 72–88px 800 · never under 32px, never in a sentence.
- Max 3 text sizes inside one tile.

## Tile anatomy
- Padding 28px (24px under 600px). Title top-left, Doto number centered, one caption line under it, "+" chip top-right (40px circle, paper).
- Summary (closed state) shows: title · Doto number · caption · "+" chip. Hide the default marker (summary::-webkit-details-marker and list-style:none).
- Open state: the "+" chip rotates 45° into "×" over 300ms; the body text fades in (opacity 0→1, translateY 8px→0, 300ms).
- Aura tile min-height 300px at 1280, 240px at 390. Paper tiles have no min-height.

## Grid at 1280 (no holes: each row's spans add up to 12)
- Row 1: hero span 8 (label, name, hook, stat row) + CareerOS aura tile span 4.
- Row 2: XPT OS span 4 · Food Pantry span 4 · ĀRIAxĒRON span 4.
- Row 3: Who I am span 7 (paper) · How I work span 5 (tile bg, "Verification over vibes." at 40px Geist 400).
- Row 4: Reach me span 12: four paper pills in one row, each at least 44px tall.
- Under 600px the order is hero, CareerOS, XPT, Food Pantry, ĀRIA, Who, How, Reach.

## States
- Hover (only under @media (hover:hover)): tile lifts translateY(-3px) and the shadow deepens to 0 18px 40px rgba(0,0,0,.07) over 200ms cubic-bezier(.2,.8,.2,1). Pills: background moves to --tile.
- Focus-visible: 2px solid var(--ink) outline, 3px offset, following the tile radius. It must be visible on aura tiles in both modes.
- Links inside tiles: --ink, 1px underline, 0.2em offset. No brown, no blue.

## Motion budget
- At most 2 continuous animations visible on one screen: the Live ping plus one aura breathe at a time (stagger with animation-delay).
- Breathe is 8–10s, scale 1→1.06, never faster.

## Done check (screenshots)
- Attach 390 and 1280, light and dark (4 PNGs). Each must show: Doto numbers rendered (not monospace fallback), aura glow visible, no empty grid cell, no overlapping text.
