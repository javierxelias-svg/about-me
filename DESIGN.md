# DESIGN.md: the look of this page

**The approved look is in `design/`:** four screenshots (`approved-v8-*.jpg`: 1280 and 390 wide, light and dark) plus `design/GLASS-RECIPE.md`, which has the exact values. Match the screenshots' look. Two notes: the screenshots predate some copy changes, so all text comes from `content.md` (see "Content per tile"); and the recipe's review-only `#motion` hook and `glass.js` are not part of the build.

**In one line:** clear Apple-style liquid glass tiles over soft colored light, thin dot-matrix numbers, light Geist type, calm and minimal. No rainbow or RGB-split edges, no neon, no 3D tilt, no cursor-following spotlight, no plain 1px white borders.

## Type (self-hosted in /fonts)
- Geist for all text: body 17px weight 300, line-height about 1.6; h1 `clamp(38px, 4.6vw, 58px)` 300, letter-spacing -0.045em; hook 20px; h2 22px 400; captions 15px 400; project one-liners 14px 400; pills and labels 13px 400; "Verification over vibes." 30px 300.
- Doto (dot-matrix) only for big numbers: hero stats `clamp(30px, 2.9vw, 38px)` 700; tile numbers 60px 700 (52px under 600px wide). Every tile's text uses the body size; no smaller override. Small "of" and the arrow are Geist. Digits catch a light edge (recipe, "Glass digits").
- Load only the font files actually used. Generous spacing: text must never feel squished (recipe v6 spacing values).

## Color and themes
- Light canvas #E4E4E2, dark canvas #0C0D0F. Every color is a token written once with `light-dark()`.
- Theme follows `prefers-color-scheme`, and a **CSS-only switch** (native `<input type="checkbox" role="switch">` + `:has()`) flips it both ways. The switch is a glass pill with sun and moon beads, top-right of the hero, at least 44px tall, with a visible focus ring. Its accessible name says what it does.
- Project colors: CareerOS green, XPT OS orange, Food Pantry pink, ĀRIAxĒRON sky and violet.

## Glass (every tile)
- Clear glass: a low fill (light about 34% white, dark about 52%), `backdrop-filter: blur(22px) saturate(160%)` (with the `-webkit-` prefix). At most 12 elements use backdrop-filter.
- Thickness: a bright outer line + a dark line just inside it, a refraction band, a soft lift shadow. Subtle, not a raised lip.
- Reflection: a curved highlight across the upper part of each tile, plus faint grain.
- Every glow ends inside its own box; no gradient may be cut off in a straight line at the grid edge.
- **Color sits behind the glass**: each project's light is a layer under the grid, bigger than its tile, so the blur shows real color and the tile edge cuts it (recipe: anchor positioning, with the in-tile glow as fallback). A soft five-color mesh sits behind the whole page.
- Project tile rims pick up their project color toward the bottom; plain tiles stay neutral.
- The "+" buttons and switch knob are glossy glass beads; the pills get a flatter version.

## Motion (CSS only)
- Signature: a soft light arc travels around each tile's rim (12s loop, phase offset by column and row so it reads as one light crossing the page). On hover, one quick extra lap is added on top of the running loop, so the arc lands back in phase: no snap, no freeze. If it can't be seamless, drop the hover lap.
- One wide, soft band of light drifts slowly behind the grid (36s, transform only).
- The "Live" dot pings using transform and opacity (not box-shadow).
- Everything that moves stops under `prefers-reduced-motion: reduce`. Write that rule on its own, so a selector the browser doesn't know can't cancel it.

## Layout: 12-column grid, gap 16px, max-width 1200px
The order puts the promise and its proof first: the full "Why this job" sentence must be visible without scrolling at 1280×800 and 390×844.
1. Hero (span 8): label = the "Hero line" from content.md, h1, hook, two Doto stats (341 boards, 16,129 listings), theme switch. CareerOS tile (span 4) next to it. This row looks exactly like `design/approved-v8-1280-*`.
2. XPT OS · Food Pantry · Why this job (span 4 each).
3. How this page was built (full width): intro paragraph on the left, five numbered steps on the right, each ending in its proof link.
4. ĀRIAxĒRON (span 4) | Who I am (span 8).
5. How I work (full width).
6. How to reach me (full width): four glass pills.
7. Footer line, centered, small.
- The rim-light phase follows this order, so the light still reads as one light crossing the page.
- Under 960px tiles span 6 ("Why this job" spans 12, right after CareerOS); under 600px everything stacks in this order. On phones, leave clear space between a tile's title row and its big number. No sideways scroll at 320px.

## Content per tile (all text from content.md, word for word)
- Project tiles are `<details>`. The closed state shows the title, the big number, a caption and the one-liner; opening shows the full paragraph and the link.
- CareerOS: big "161 of 162" (the cloud-runs figure in content.md), caption "cloud runs succeeded", one-liner = the "CareerOS line".
- XPT OS: big "17", caption "weekly runs", one-liner "The cloud sandbox blocked outbound calls, so a git commit became the message queue."
- Food Pantry: big "1 → 0", caption "file → logins" (from the "Food Pantry line"), one-liner "Turns a pantry's weekly numbers into a plain-English brief for volunteers."
- ĀRIAxĒRON: big "61", caption "applicants in a month", one-liner "Founded an email marketing company for luxury hotels."
- Who I am, Why this job, How I work, How this page was built (intro + steps 01–05 with their proof labels), How to reach me, Footer: from their sections in content.md.
- Proof links: step 01 → commit 2714ab4 (the checker and its first failing run), 02 → pull request #1, 03 → commit 526dfcb, 04 → commit abbe230, 05 → pull request #2 (all on github.com/javierxelias-svg/about-me). Where step text names a file, link that word to the file on main, with no new words: "AGENTS.md" → blob/main/AGENTS.md, "DESIGN.md" → blob/main/DESIGN.md, "content.md" → blob/main/content.md. Keep each label's text exactly as content.md writes it after "Proof:". Put spaces or separate block elements between pieces of text so words never run together.

## Quality must-haves
- A favicon (an inline SVG data URI or a small file in the repo), `theme-color` for both themes, and og/twitter meta (title + description from content.md).
- `@media (forced-colors: active)`: tiles keep visible edges, and "+" and the switch stay visible.
- `@media (prefers-reduced-transparency: reduce)`: tiles become solid, readable surfaces.
- Tap targets at least 24px, including the proof links. Text contrast at least 4.5:1 everywhere, including over glass and color.
- style.css reads top to bottom: tokens, base, layout, glass, components, motion, media queries. One section header comment each. No duplicate overriding blocks, no dead rules.
