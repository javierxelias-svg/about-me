# AGENTS.md: rules for any agent working in this repo

This repo is my About Me page for the Fredrin Forward Deployed Engineer application. Read this file before every ticket.

## Facts
- Every sentence, name, date and number on the page comes from `content.md`. Nothing is added, rounded or reworded.
- Never edit `content.md`. If something there looks wrong, say so in your final message and stop.
- My name is "Javier Elías Crespo", always with the accent on the í.

## Stack
- Plain HTML and CSS only: `index.html` + `style.css`. No JavaScript, no build step, no framework, no package.json.
- Nothing loads from another site: no Google Fonts, no icon libraries, no CDNs, no analytics. Fonts are self-hosted in /fonts.
- Interactive parts use native HTML (`<details>` / `<summary>`).
- Published by GitHub Pages from `main`, root folder. Links must work from that path.

## Design bar
- **The look is defined in DESIGN.md (my Day One style). Follow it exactly; don't invent a different style.**
- Body text 18–20px equivalent, line height about 1.5, running text no wider than about 65 characters.
- Light and dark mode through `prefers-color-scheme`. Text contrast at least 4.5:1 in both.
- Semantic HTML: `header`, `main`, `section`, `footer`; exactly one `h1`; headings in order; `lang="en"`.
- No sideways scroll at 320px wide. Visible focus outline. Tap targets at least 24px.
- Outside links open in a new tab with `rel="noopener"`. The email link uses `mailto:`.
- CSS animations only, all turned off under `prefers-reduced-motion: reduce`.
- No skill bars, stock photos, icon libraries or testimonials.

## Done means
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
