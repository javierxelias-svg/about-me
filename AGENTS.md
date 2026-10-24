# AGENTS.md: rules for any agent working in this repo

This repo is my About Me page for the Fredrin Forward Deployed Engineer application. Read this file before every ticket.

## Facts
- Every sentence, name, date and number on the page comes from `content.md`. Nothing is added, rounded or reworded.
- Never edit `content.md`. If something there looks wrong, say so in your final message and stop.
- My name is "Javier Elías Crespo", always with the accent on the í.

## Stack
- Plain HTML and CSS: `index.html` + `style.css`. No build step, no framework, no package.json.
- JavaScript: one optional file only, `glass.js` (≤2 KB, self-hosted), loaded as exactly `<script src="glass.js" defer></script>`, used only for the pointer light on the glass. The page must look and work the same with it turned off. Nothing else runs script.
- Nothing loads from another site: no Google Fonts, no icon libraries, no CDNs, no analytics. Fonts are self-hosted in /fonts.
- Interactive parts use native HTML (`<details>` / `<summary>`).
- Published by GitHub Pages from `main`, root folder. Links must work from that path.

## Design bar
- **The look is defined in DESIGN.md (my Day One style). Follow it exactly; don't invent a different style.**
- Type sizes, weights and spacing come from DESIGN.md. Running text no wider than about 65 characters.
- Light and dark mode: follow `prefers-color-scheme`, plus the CSS-only switch in DESIGN.md that flips it. Text contrast at least 4.5:1 in both, including text over glass and color glows.
- Semantic HTML: `header`, `main`, `section`, `footer`; exactly one `h1`; headings in order; `lang="en"`.
- No sideways scroll at 320px wide. Visible focus outline. Tap targets at least 24px.
- Outside links open in a new tab with `rel="noopener"`. The email link uses `mailto:`.
- Motion is CSS (plus the optional pointer light). Everything that moves is turned off under `prefers-reduced-motion: reduce`.
- No skill bars, stock photos, icon libraries or testimonials.

## Done means
Attach screenshots at 390px and 1280px wide, light and dark (4 images), and compare them with the approved look in `design/` before finishing.
Run the checker and paste its full output in your final message:

```
python3 /tmp/fredrin-kit/verify_page.py . --content content.md
```

Fix every FAIL before finishing. List every WARN and say why it's acceptable or what you changed.

## Never
- Never edit, skip or weaken the checker, tests or CI to make them pass.
- Never touch files the ticket doesn't need.
- Never run `git stash` (it's shared across every Fredrin worktree).
- Don't assume. If the ticket is unclear, ask before building.
