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
   **Stat row** in Doto (clamp(40px, 5vw, 58px)), each number with a small pill label: **341** boards · **16,129** listings · **158/159** cloud runs. A lime "Live" chip with a pinging dot next to CareerOS.
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
