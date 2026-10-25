# Glass recipe (approved prototype values, 10-24)

Values measured in the approved prototype. Build the page from DESIGN.md; use this file for exact numbers. The prototype's review-only `#motion` hook and `glass.js` are NOT part of the build.

# v8 notes: clear glass + rim light (recipe for DESIGN.md)

Prototype only. v8 = v7 + the owner's reference picks (YES 01, 02, 04, 05, 07, 10, 14; NO 03, 06, 08, 09).
Same content, fonts, sizes, layout, switch beads. **Zero JavaScript**: `glass.js` is not loaded and not shipped.
All v8 CSS is the "v8" block at the end of `style.css` (it overrides v7; the v7 recipe below still describes
the layers v8 keeps: page mesh, project lights behind the glass via CSS anchors, reflections, bevel).

(`#` not `?`: with zero JS, CSS can only read the URL fragment, through `:target`. It needs one hidden element
in the HTML, `<i id="motion" hidden></i>`, plus 2 CSS rules. **Remove both in production.**)

## v8 changes, exact values
**Removed:** the stripe that crossed the tile face (ref 06 = NO) and the cursor-following light (it needed JS and
reads as the Framer "Glow Bento" template). The tile face is only reflection + grain + frost now.

**1. Rim light, the signature motion (ref 05)** on `.tile::after`:
- `@property --a { syntax: "<angle>"; inherits: false; initial-value: 0deg; }`
- `background: conic-gradient(from var(--a), transparent 0deg 272deg, arc-soft 308deg, arc 334deg, transparent 354deg)`
  (a soft ~80° arc, brightest at the leading end).
- Shape: no box mask. Four mask layers make a crisp 1.5px edge line plus a soft 12px glow into the glass:
  `--edge: #000 0 1.5px, rgba(0,0,0,.32) 1.5px, transparent 12px;`
  `mask: linear-gradient(to bottom, var(--edge)), linear-gradient(to top, …), linear-gradient(to right, …), linear-gradient(to left, …)` (same for `-webkit-mask`).
  Without the glow the arc is invisible in light mode (a white line on a white edge).
- `z-index: 4; inset: 0; border-radius: inherit`.
- Colors: neutral tiles: arc white 1 / .75, arc-soft white .45 / .22. Project tiles (ref 14): arc =
  `color-mix(in srgb, var(--glow) 55%, white)` light / 70% dark, arc-soft = glow 45%.
- Motion: `animation: rim 12s linear infinite` (`@keyframes rim { to { --a: 360deg } }`),
  `animation-delay: calc(var(--c) * -.45s + var(--r) * -.9s)`. The negative delay is a phase offset by grid
  column/row (same `--c`/`--r` table as v7), so the arcs form one travelling wave across the page with no waiting.
- Hover: `animation: rim-pass 1.6s cubic-bezier(.4,0,.2,1) 1` (one quick lap).
- Reduced motion: no animation; the arc rests at `--a: 0deg` (top-left, where the light is).
- Cost (honest): an `@property` angle repaints the arc every frame (not compositor-only). 10 small layers; fine on a
  Mac, test on an older phone. Fallback without `@property`: the arc sits still (the page still looks complete).

**2. Light drifting behind the grid (ref 07)** on `body::after` (one layer, transform only):
`position: fixed; z-index: -1; top/bottom: -20vh; left: 0; width: 70vw;`
`background: linear-gradient(90deg, transparent, drift 50%, transparent)`, drift white .55 / .045;
`animation: drift 36s ease-in-out infinite alternate` from `translateX(-60vw) skewX(-16deg)` to `translateX(95vw) skewX(-16deg)`.
Seen only through the frosted tiles and gutters. Reduced motion: parked at `translateX(10vw)`.

**3. Clearer glass (ref 04).** Fill `--tile` rgba(246,246,244,.34) / rgba(20,22,26,.52) (v7 .48 / .62);
`--paper` rgba(250,250,249,.38) / rgba(24,27,31,.52); frost-top white .20 / .03. Color comes only from behind.
One soft local scrim where small grey text sat on strong color on phones: `.steps` (build log) gets
`background: veil; box-shadow: 0 0 22px 10px veil; border-radius: 16px`, veil white .26 in light, none in dark.

**4. Rim takes the color behind (ref 14).** Project tiles' `::before` ring:
`linear-gradient(135deg, color-mix(glow 35%, rim-hi) 0%, rim-mid 28%, transparent 55% 72%, color-mix(glow 45%, rim-back) 100%)`
over `linear-gradient(180deg, transparent 15%, glow 22% 60%, glow 34%)` (alpha mixes), so the rim is neutral at
the top and picks up the project light toward the bottom where it sits. Plain tiles keep the v7 neutral rim +
mesh ring. Static rim softened so the moving arc stands out: rim-hi white .75 / .30, rim-mid .20 / .06, bev-hi white .80 / .13.

**5. Thickness (ref 02/10), kept subtle (03/09 = NO):** `--thick` = hairline .5px, bevel (v7), dark line
`inset 0 0 0 2.5px` (v7), refraction band `inset 10px 10px 18px -12px` band-hi + `inset -10px -10px 18px -13px` band-lo
(slightly deeper than v7), inner glow 22px. Lift shadow unchanged.

**6. Glass digits (ref 01)** on `.dot-num, .big`: color #2A2C30 / #D9DDE3 (large text, needs 3:1; measured ≥5.18:1),
`text-shadow: -.75px -.75px 0 hi, .75px .75px .5px lo`, hi white .95 / .85, lo rgba(30,34,40,.10) / black .45.
Each dot catches a light edge top-left. `.of` and the arrow get `text-shadow: none`.

## v8 checks
contrast.py, minimum normal-text ratio, 0 failures in all 12:
| Width | Light closed | Light open | Dark closed | Dark open |
|---|---|---|---|---|
| 1280 | 4.74 | 4.75 | 5.38 | 5.42 |
| 390 | 4.80 | 4.75 | 5.30 | 5.30 |
| 320 | 4.80 | 4.72 | 5.26 | 5.26 |
First pass failed light 390/320 (build log `.when`/`.proof` 4.41–4.42); the `.steps` scrim fixed it.
Drift worst case (temporary copy, deleted: the band's peak color over the whole screen): 0 failures,
light 5.22 / 5.31 / 5.31, dark 5.30 / 5.00 / 5.01 (1280 / 390 / 320). The rim arc's glow stops 12px inside the edge,
and text starts 24–36px in, so it never sits behind text.
- No sideways scroll (scrollWidth = clientWidth at 320/390/1280). backdrop-filter: 11. Scripts on the page: 0.
- Bytes: style.css 42,039 (10,452 gzipped), index.html 9,368.
- DuckDuckGo (WebKit): renders, anchored lights and colored rims show, no horizontal scrollbar; with `#motion`
  two screenshots 2s apart differ in 8,694 pixels (the arcs and band are moving).
- Shots: `shots/v8-1280-{light,dark}.png`, `v8-390-{light,dark}.png`, `v8-rim-strip-{light,dark}.png` (t = 2s, 5s, 8s).

## v8 self-critique
- The rim arc is clear in dark and on colored tiles. On light plain tiles it is a soft glow and easy to miss.
- The project tiles' rims now read close to a colored border at the bottom. That is ref 14, but it is the line
  between "glass catching color" and "outlined card". Lower the 34% bottom stop if he finds it heavy.
- The fill is clearer but still frosted. Going lower fails 4.5:1 on phones without more scrims.
- `#motion` and the hidden `<i>` are review-only. Remove them in production.

---

# (base) v7 notes: sliding glass + glass slabs (recipe for DESIGN.md)

Prototype only. v7 = v6 with the same content, fonts, sizes, layout, Doto numbers and glass-bead switch.
Everything new sits in the "v7" block at the end of `style.css` (it overrides v6 values; read v6/NOTES.md
for the layers v7 keeps: grain, frost veil, lift shadows, beads, pills, switch).
`index.html` is byte-identical to v6. `glass.js` gained 2 lines (a review-only `?motion` switch, below).


## Light model
One light, top-left. Every highlight is brighter top-left, every shade deeper bottom-right, and the
moving glint is that same light crossing the page left to right.

## 1. Behind the glass (what the blur shows)
**a. Page mesh** (`body::before`, fixed, static). Five soft radial gradients from the tile colors:
| Blob | Size, position | LIGHT | DARK |
|---|---|---|---|
| leaf | 44vmax 34vmax at 86% 6% | rgba(120,190,120,.30) | rgba(52,211,90,.10) |
| sky | 40vmax 38vmax at 6% 20% | rgba(141,176,240,.32) | rgba(111,155,255,.11) |
| rose | 36vmax 30vmax at 52% 48% | rgba(240,150,190,.20) | rgba(255,74,146,.07) |
| violet | 44vmax 40vmax at 4% 84% | rgba(180,140,242,.28) | rgba(162,123,255,.12) |
| tang | 46vmax 36vmax at 84% 94% | rgba(255,170,100,.24) | rgba(255,122,15,.07) |
Each fades `transparent 70%`. Canvas color unchanged (#E4E4E2 / #0C0D0F).

**b. Project light behind each project tile** (the main "glass, not a box" cue). Each project's color is
no longer painted inside the tile. It is a layer UNDER the grid, 56px bigger than the tile on every side,
pinned to the tile with CSS anchor positioning, so the tile's `backdrop-filter` blurs real color and the
tile edge visibly cuts it (crisp color in the gutter, blurred and frosted inside).
- Anchors: `.green{anchor-name:--green}` `.orange{--orange}` `.pink{--pink}` `.orb{--orb}`.
- Carriers (no extra HTML): the spare pseudo-elements of the `display:contents` wrappers, which come after
  the tiles in the tree so the anchors resolve: `.projects::after` (green), `main::after` (orange),
  `.foot::before` (pink), `.foot::after` (ĀRIAxĒRON). `.bento{position:relative}` is their containing block.
- Box: `position:absolute; z-index:-1; top: calc(anchor(top) - 56px); bottom: calc(anchor(bottom) - 56px);
  left: max(calc(anchor(left) - 56px), -12px); right: max(calc(anchor(right) - 56px), -12px)`.
  The `max(…, -12px)` keeps the light inside the body gutter, so there is never a sideways scroll.
- Fill: `radial-gradient(closest-side at 50% 56%, core, mid 70%, transparent 100%)`;
  core = glow color 80% (light) / 78% (dark), mid = 50% / 46% (via `color-mix(in srgb, var(--glow) N%, transparent)`).
- ĀRIAxĒRON: `radial-gradient(42% 40% at 38% 48%, sky core, transparent)` over
  `radial-gradient(46% 44% at 64% 62%, violet core, violet mid 60%, transparent)`.
- Inside `@supports (anchor-name: --a)` only. Without anchor support (older browsers) the v6 in-tile glows
  and halos stay as the fallback. Verified working in DuckDuckGo (WebKit, macOS 26) and Chrome.
- Project tiles then use the plain glass fill (`--tile`) plus the colored refraction band
  `inset 0 -18px 30px -20px` glow 60% / 45% (from v6). The v6 outer halos are dropped (the light does it).
- The light follows the tile when a project opens (anchors track layout).

## 2. Tile = glass slab (every `.tile`)
| Layer | LIGHT / DARK |
|---|---|
| Fill `--tile` | rgba(244,244,242,.48) / rgba(22,24,28,.62); `--paper` (.who) rgba(250,250,249,.54) / rgba(28,31,36,.62) |
| Backdrop | `blur(22px) saturate(160%)` (v6). backdrop-filter elements: 11 (10 tiles + switch track) |
| Top reflection | `radial-gradient(95% 90% at 28% -52%, refl 0%, refl-2 90%, transparent 97%)`: a broad dome whose lower edge curves from ~38% of the tile height at the left to ~7% at the right. refl white .62 / .065, refl-2 white .30 / .035. The 90→97% stop gives a soft but readable edge (not a hard 2008 gloss line) |
| Bottom bounce | `radial-gradient(64% 26% at 70% 104%, transparent 50%, refl-low 78%, transparent 100%)`: a thin crescent of light just above the bottom edge (light bouncing inside the slab). refl-low white .55 / .04 |
| Frost veil | `linear-gradient(180deg, frost-top, frost-low 55%)`, frost-top white .26 / .035 (v6), frost-low white .03 / 0 (clearer low half) |
| Grain | v6 SVG noise, unchanged |
| Rim + refraction ring (`::before`) | 3px ring (`padding:3px` + the v6 mask: `mask: linear-gradient(#000 0 0) content-box exclude, linear-gradient(#000 0 0)`; `-webkit-mask-composite: xor`), `opacity:.85`. Two layers: (1) v6 rim gradient `linear-gradient(135deg, rim-hi, rim-mid 28%, transparent 55%, transparent 72%, rim-back)`; (2) the SAME five mesh blobs with `background-attachment: fixed` (so they line up with what is behind) at higher strength: leaf rgba(110,200,110,.55)/.16, sky rgba(120,165,245,.55)/.17, rose rgba(245,130,180,.40)/.12, violet rgba(170,125,245,.50)/.17, tang rgba(255,160,80,.45)/.12. Reads as the background bent and concentrated at the edge |
| Bevel | `inset 1px 1px 0 bev-hi, inset -1px -1px 0 bev-lo, inset 2px 2px 2px -1px bev-hi2, inset -2px -2px 2px -1px bev-lo2`. bev-hi white .95 / .13, bev-hi2 white .50 / .05, bev-lo rgba(40,46,60,.13) / black .60, bev-lo2 rgba(40,46,60,.06) / black .30 |
| Line inside the rim | `inset 0 0 0 2.5px` rgba(20,24,30,.05) / black .28 |
| Refraction band | `inset 8px 8px 14px -10px` white .70 / .06 (lit side), `inset -8px -8px 16px -12px` rgba(20,24,30,.10) / black .35 (far side) |
| Hairline + inner glow | `inset 0 0 0 .5px` hairline, `inset 0 0 22px` inner (v6 values) |
| Lift | v6 `--lift` unchanged |
Full `--thick` order: hairline, bevel (4), line-in, band-hi, band-lo, inner glow.

## 3. The sliding glint (`.tile::after`)
- Element: `position:absolute; z-index:-1` (inside the tile's stacking context: above the glass, UNDER the text);
  `top:-20%; bottom:-20%; left:0; width:240px`.
- Paint: wide band `linear-gradient(90deg, transparent, glint-soft 30%, glint 50%, glint-soft 70%, transparent) 0 0 / 150px 100% no-repeat`
  plus a thin trailing streak `linear-gradient(90deg, transparent, glint 50%, transparent) 186px 0 / 22px 100% no-repeat`
  (two parallel streaks = a pane of glass). glint white .60 / .045, glint-soft white .22 / .025.
- Angle: `skewX(-18deg)` on every tile (same light everywhere).
- Motion: `transform` only. Keyframes `glint`, 12s, linear, infinite:
  0% `translateX(-420px) skewX(-18deg)` → 20% `translateX(1500px) skewX(-18deg)` → 20.01–100% back at -420px (off-tile, invisible).
  Sweep = 2.4s at a constant 800px/s, so the light moves at the same speed over every tile, whatever its width.
- One light across the page: `animation-delay: calc(.6s + var(--c) * .12s + var(--r) * .16s)`.
  `--c` = the grid column the tile starts in (0–11), `--r` = its row. .12s per column ≈ 101px at 800px/s, so the
  band reaches each tile when the page-wide light would; the row term follows the band's slant.
  ≥960px: hero c0 r0, CareerOS c8 r0, XPT c0 r1, Pantry c4 r1, ĀRIAxĒRON c8 r1, Who c0 r2, Why c6 r2, How c6 r3, Build r4, Reach r5.
  600–959px: CareerOS c0 r1, XPT c6 r1, Pantry c0 r2, ĀRIAxĒRON c6 r2, Who c0 r3, Why c6 r3, How c6 r4, Build r5, Reach r6.
  <600px: all c0, r = order 0–9 (the light runs down the single column).
- Plays once ~0.6s after load, then every 12s. Hover (pointer devices): the same band sweeps again via the
  separate `translate` property, `translate: 1920px 0` over 2.2s `cubic-bezier(.3,.1,.4,1)` (does not disturb the loop).
- `prefers-reduced-motion: reduce`: the glint is `display:none` (v6 rule) and all animation is off.
- Review-only: `glass.js` sets `data-motion="on"` when the URL has `?motion`, and one CSS rule re-enables the glint
  under reduced motion for that case. **Drop both in production** (or keep as an explicit opt-in).

## 4. Text and small changes
- Dark `--soft-text` #9AA0A7 → #A4AAB1 (one step lighter) so secondary text stays ≥4.5:1 while the glint passes.
- Dark pills: `--pill-cap` white .06 → .03, `--pill-body` .035 → 0 (the pill reads by its rim; fixed a 4.36 fail at 390px).
- Pointer spotlight (glass.js) is unchanged and optional; the page is complete without JS. The reference research
  flags the cursor spotlight as the "Framer Glow Bento" signature: consider dropping it.

## Checks
contrast.py, minimum normal-text ratio, 0 failures in all 12:
| Width | Light closed | Light open | Dark closed | Dark open |
|---|---|---|---|---|
| 1280 | 4.77 | 4.72 | 5.55 | 5.51 |
| 390 | 4.58 | 4.59 | 5.19 | 5.19 |
| 320 | 4.58 | 4.58 | 5.23 | 5.23 |
Note: headless Chrome reports `prefers-reduced-motion: reduce`, so contrast.py measures without the glint.
Extra worst case (temporary copy, deleted): the glint's PEAK color spread over every whole tile, i.e. every
text run sitting on the brightest part of the band. 0 failures: light 5.59 / 4.96 / 4.96, dark 4.90 / 4.53 / 4.60 (1280 / 390 / 320).
- No sideways scroll: scrollWidth = clientWidth at 320, 390 and 1280 (the lights clamp to the gutter).
- backdrop-filter: 11 (limit 12). The lights, ring and glint add none.
- Bytes: style.css 36,224 (9,148 gzipped; v6 26,110 / 6,772), index.html 9,283 (unchanged), glass.js 868 (17 lines).
- Switch path: a temporary copy with the switch pre-checked, under dark system, rendered the same file as the
  light-scheme render (2,150,772 bytes both); deleted after.

## Shots (`shots/`, Chrome headless, motion off)
v7-1280-light.png, v7-1280-dark.png, v7-390-light.png, v7-390-dark.png (390 via iframe).
v7-glint-strip-light.png, v7-glint-strip-dark.png: three moments of one sweep (t = 1.1s, 1.6s, 2.25s after load),
glint frozen with a temporary copy. In the light strip the band visibly travels hero → CareerOS; in dark it is faint.

## Honest self-critique vs Apple's clear Liquid Glass
What got closer:
- The color is finally BEHIND the glass. Gutters show crisp color, the tile shows it blurred and frosted, and
  neighbors pick up a tint at their edges. This is the biggest step from v6 and it works in his browser.
- Every tile, including the plain ones, now has readable thickness: lit top-left edge, dark far edge, a darker
  line inside the rim, a curved reflection on top and a crescent near the bottom.
- The glint reads as one light crossing the page, at one angle and one speed.
What is still not Apple:
- **His Mac has Reduce Motion on**, so at the plain URL he will not see the glint at all (correct behavior).
  He must use `?motion` to judge it.
- **Dark-mode glint is faint** (peak white .045). That cap comes from keeping secondary text ≥4.5:1 while the band
  passes. Brighter would look better and dip below 4.5 for ~0.1s per pass. The light-mode glint is clearly visible.
- **Light mode is still frosted, not clear.** Apple's clear look is far more transparent; ours keeps ~48% white fill
  so text holds 4.5:1 over color. It reads as premium frosted glass over color, not as liquid.
- **No true refraction.** The edge ring fakes lensing with a saturated copy of the mesh; content behind is not bent.
  (`feDisplacementMap` backdrop works only in Chromium, not in his WebKit browser.)
- **The bottom crescent repeats** identically on all 10 tiles; on short tiles (Why this job) it is a little heavy.
- **`background-attachment: fixed` on the ring** repaints on scroll in Chrome and is ignored on iOS Safari (the ring
  then shows a stretched slice of the mesh, still colored, just not aligned). Test scrolling on an iPhone.
- **Anchor positioning is new** (Chrome 125+, Safari 26+). Older browsers get the v6 in-tile glows instead,
  which is fine but less glassy.
- Not built (optional ref #05): a highlight travelling around the rim (`@property` conic sweep). Skipped to keep
  motion to one idea and repaints low; easy to add if he wants more life.

---

# v6 notes: the liquid-glass recipe (for DESIGN.md)

Prototype only. v6 = v5 with the same layout, content, fonts, sizes and Doto numbers. Only the glass,
the color, the + beads, the switch and spacing changed. All v6 CSS sits in the "v6" block at the end
of `style.css` (plus 3 small edits above it: the old glow pseudo-element removed, the `.toggle`
blur removed, the header comment). `index.html` and `glass.js` are unchanged from v5.


## Light model
One light source, top-left. Everything below follows from it.

## 1. Tile = a slab of glass (every `.tile`)
| Layer | Value (LIGHT / DARK) |
|---|---|
| Fill | `--tile` rgba(241,241,239,.66) / rgba(22,24,28,.66); `.paper` .72 / .70 (from v5) |
| Backdrop | `blur(22px) saturate(160%)` (from v5) |
| Frost veil (bg layer) | `linear-gradient(180deg, frost-top, frost-low 55%)`, top white .26 / .035, low white .10 / 0 |
| Specular cap (bg layer) | `radial-gradient(130% 62% at 22% -12%, spec, transparent 64%)`, spec white .62 / .055. Soft, no hard edge (hard = 2008 gloss) |
| Grain (bg layer) | inline SVG data-URI `feTurbulence` fractalNoise, baseFrequency .9, 2 octaves, 180px tile, gray .5 at alpha 0–9% |
| Pointer light (bg layer) | `radial-gradient(320px circle at --mx --my, spot, transparent 70%)`, spot white .55 / .07 (v5) |
| Gradient rim (`::before`) | 1px, mask trick (`padding:1px` + `mask: linear-gradient(#000 0 0) content-box exclude, linear-gradient(#000 0 0)`, `-webkit-mask-composite: xor`). `linear-gradient(135deg, rim-hi 0%, rim-mid 28%, transparent 55%, transparent 72%, rim-back 100%)`. rim-hi white 1 / .30, rim-mid .35 / .06, rim-back .70 / .10 (faint re-catch bottom-right) |
| Thickness (inset shadows) | `inset 0 0 0 .5px` hairline rgba(30,34,40,.06) / white .03; `inset 0 1px 0` top edge white .95 / .14; `inset 0 -1px 0` bottom edge rgba(40,44,52,.10) / black .55; `inset 0 0 22px` inner glow white .55 / .035 |
| Lift (outer shadows) | `0 1px 1.5px L1, 0 6px 16px -6px L2, 0 26px 56px -24px L3`; L1/L2/L3 = rgba(30,34,40,.05/.07/.08) / black .35/.40/.45 |
| Padding | 36px desktop (was 32), 24px under 600px (unchanged) |

## 2. Color = light under the glass (project tiles, `.aura`)
Color is never painted on top. Stack, top to bottom: pointer light, specular cap, grain, frost veil,
**then** the glow, then the tile's tinted base. So the frost sits over the color.
- Core glow: `radial-gradient(62% 48% at 50% 56%, g-in, transparent)`; g-in = glow at 70% / 42% alpha.
- Bottom caustic: `radial-gradient(90% 42% at 50% 108%, g-edge, transparent)`; g-edge = glow 34% / 34%.
- Refraction band (light gathered in the bottom rim): `inset 0 -18px 30px -20px` glow 60% / 45%.
- Bleed past the edges onto the canvas: `0 22px 60px -14px` glow 42% / 30%, plus `0 0 36px -8px` glow 22%. Hover: 26px 66px, 50% / 38%.
- ĀRIAxĒRON: two overlapping glows (sky at 40%/50%, violet at 62%/60%, each 44%×40%) + violet caustic.
- Plain tiles: no color, only the whisper of the specular cap and frost.
- Inner summary/body panels lost their v5 scrim (`background: none`), no more box-in-a-box.
- Canvas ambient, light only, raised so the glass has something to bend: green/violet .16 → .22, orange .10 → .14.
- Note: `light-dark()` only accepts colors, so strengths live inside each color via `color-mix`. A `light-dark(70%, 55%)` percentage silently kills the whole declaration (that was iteration 1).

## 3. Glass bead (the four "+" and the switch knob)
```
background:
  radial-gradient(110% 62% at 50% 0%,  cap,     transparent 62%),  /* top specular cap */
  radial-gradient(80% 50%  at 50% 104%, caustic, transparent 70%),  /* light focused at the bottom */
  body;
box-shadow:
  inset 0 0 0 .5px line, inset 0 1px .5px top-edge, inset 0 -1.5px 2px bottom-edge,
  0 1px 1.5px drop, 0 5px 12px -4px drop;
```
cap white .98 / .34, caustic white .85 / .16, body white .40 / .10, line rgba(30,34,40,.13) / white .16, drop rgba(30,34,40,.16) / black .55.
- "+" hover: `scale 1.07`; press: `scale .9` (90ms), release springs back with `cubic-bezier(.3,1.45,.5,1)` 260ms. Open state still rotates 45°.
- Switch track: recessed, `rgba(30,34,40,.05)` / white .05, `inset 0 1px 2px` shadow, 0.5px line, 1px bright lip under it. Backdrop blur kept (v5).
- Switch knob: `.theme::before`, the bead, 36px, slides 38px with `translate 460ms` spring overshoot; stretches `scale 1.14 .9` while pressed (liquid squish). Position is pure CSS: `prefers-color-scheme` × `:has(#theme:checked)`.
- Pills (boards/listings, reach links): the same bead, flatter: cap white .98 / .06, body .40 / .035 (dark kept low for contrast), lift on hover 1px, press 1px down.

## 4. Motion
Hover sheen sweep (v5, `::after`, 760ms), pointer light (glass.js, unchanged, 15 lines), press squish on beads/knob/pills.
`prefers-reduced-motion: reduce` turns off all animation/transition, the sheen, the knob slide and the squish.
Removed: the 40s "breathe" on the glow (glow is now a background layer; calmer).

## 5. Spacing (same fonts, same sizes)
body line-height 1.55 → 1.6; paragraph gap 12 → 16px; h2 bottom 16 → 18px; hook 1.45 → 1.5;
project one-liner 1.4 → 1.5; build steps padding 14 → 16px; build intro 1.65; summary row gap 8 → 10px; How-I-work gap 12 → 14px; tile padding 32 → 36px (desktop).

## Contrast (contrast.py, worst pixel behind every text run, 3 phases)
| Width | Light closed | Light open | Dark closed | Dark open |
|---|---|---|---|---|
| 1280 | 5.01 · 0 fail | 5.05 · 0 fail | 5.10 · 0 fail | 5.04 · 0 fail |
| 390 | 4.94 · 0 fail | 4.95 · 0 fail | 4.72 · 0 fail | 4.67 · 0 fail |
| 320 | 4.94 · 0 fail | 4.95 · 0 fail | 4.79 · 0 fail | 4.74 · 0 fail |

Fixed along the way: dark "boards"/"listings" pills failed at 2.39 with the full bead highlight; the pill cap/body were dimmed in dark only (.34 → .06, .10 → .035).

## Budgets
- backdrop-filter elements: **11** (10 tiles + switch track), limit 12. The "+" beads use no blur.
- No sideways scroll: scrollWidth = clientWidth at 320, 390, 1280.
- Bytes: index.html 9,283 · style.css 26,110 (6,772 gzipped) · glass.js 682 · fonts 64 KB (unchanged). One script. No external assets; the grain is an inline data-URI SVG.

## Checked in his browser

## What CSS alone can't do (honest)
- **No true refraction.** Apple's glass bends the content behind it at the edges (lensing). On the web that needs an SVG `feDisplacementMap` inside `backdrop-filter: url(#…)`, which works in Chromium only; WebKit (his DuckDuckGo, Safari, every iPhone) ignores it. The refraction here is faked with the colored inset band and the bottom caustic.
- **No real backdrop to bend.** The canvas is calm on purpose, so the blur mostly shows soft ambient color and the neighbors' halos, not imagery. Glass reads strongest in dark mode; in light mode it reads as premium frosted glass, less as liquid.
- **Light doesn't move with the device.** Apple tilts highlights with the gyroscope. Here only the pointer light follows the mouse (glass.js); the specular cap is fixed.
- **Knob doesn't morph** into a drop while sliding the way iOS does; it stretches on press and springs on release, which is the CSS-only approximation.
- Glow sizes are percentages of the tile, so an opened tile spreads its glow taller (looks fine, but it is not fixed-size light).
